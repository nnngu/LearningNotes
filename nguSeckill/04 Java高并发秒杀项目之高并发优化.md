# 04 Java高并发秒杀项目之高并发优化
Github：[https://github.com/nnngu](https://github.com/nnngu)   
项目源代码：[https://github.com/nnngu/nguSeckill](https://github.com/nnngu/nguSeckill)  

---

## 关于并发

并发性上不去是因为当多个线程同时访问一行数据时，产生了事务，因此产生写锁，当一个获取了事务的线程把锁释放，另一个排队线程才能拿到写锁，QPS(Query Per Second每秒查询率)和事务执行的时间有密切关系，事务执行时间越短，并发性越高，这也是要将费时的 IO 操作移出事务的原因。

### 项目中的高并发发生在哪？

下图中，红色的部分就表示会发生高并发的地方，绿色部分表示对于高并发没有影响。

![][1]

### 为什么要单独获取系统时间？

这是为了我们的秒杀系统的优化做铺垫。比如在秒杀还未开始的时候，用户大量刷新秒杀商品详情页面是很正常的情况，这时候秒杀还未开始，大量的请求发送到服务器会造成不必要的负担。

![][2]

我们将这个详情页放置到CDN中，这样用户在访问该页面时就不需要访问我们的服务器了，起到了降低服务器压力的作用。而CDN中存储的是静态化的详情页和一些静态资源（css，js等），这样我们就拿不到系统的时间来进行秒杀时段的控制，所以我们需要单独设计一个请求来获取我们服务器的系统时间。

### CDN（Content Delivery Network）的理解

![][3]

### 获取系统时间不需要优化

因为Java访问一次内存（Cacheline）大约10ns，1s=10亿ns，也就是如果不考虑GC，这个操作1s可以做1亿次。

### 秒杀地址接口分析

* 无法使用CDN缓存，因为CDN适合请求对应的资源不变化的，比如静态资源、JavaScript；秒杀地址返回的数据是变化的，不适合放在CDN缓存；

* 适合服务端缓存：Redis等，1秒钟可以承受10万QPS。多个Redis组成集群，可以到100万个QPS。所以后端缓存可以用业务系统控制。

### 秒杀地址接口优化

![][4]

### 秒杀操作优化分析

* 无法使用cdn缓存

* 后端缓存困难： 库存问题

* 一行数据竞争：热门商品

大部分写的操作和核心操作无法使用CDN，也不可能在缓存中减库存。你在Redis中减库存，那么用户也可能通过缓存来减库存，这样库存会不一致，所以要通过mysql的事务来保证一致性。

比如一个热门商品所有人都在抢，那么会在同一时间对数据表中的一行数据进行大量的`update set`操作。

行级锁在commit之后才释放，所以优化方向是减少行级锁的持有时间。

### 延迟问题很关键

* 同城机房网络（0.5ms~2ms），最高并发性是1000qps。

* update后JVM -GC(垃圾回收机制)大约50ms，最高并发性是20qps。并发性越高，GC就越可能发生，虽然不一定每次都会发生，但一定会发生。

* 异地机房，比如北京到上海之间的网络延迟，经过计算大概13~20ms。

![][5]

### 如何判断update更新库存成功？

有两个条件：

* update自身没报错；

* 客户端确认update影响记录数

优化思路：把客户端逻辑放到MySQL服务端，避免网络延迟和GC影响


### 如何把客户端逻辑放到MySQL服务端

有两种方案：

* 定制SQL方案，在每次update后都会自动提交，但需要修改MySQL源码，成本很高，不是大公司（BAT等）一般不会使用这种方法。

* 使用存储过程：整个事务在MySQL端完成，用存储过程写业务逻辑，服务端负责调用。

**接下来先分析第一种方案**

![][6]

![][7]

**根据上图的成本分析，我们的秒杀系统采用第二种方案，即使用存储过程。**

### 优化总结

* 前端控制。暴露接口，按钮防重复（点击一次按钮后就变成灰色，禁止重复点击按钮）

* 动静态数据分离。CDN缓存，后端缓存

* 事务竞争优化。减少事务行级锁的持有时间

## 下载安装Redis

Redis是一个开源的、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库

下载安装Redis的步骤，搜索引擎能找到相关的资料，本文不做展开。

下载安装完Redis之后就可以继续进行操作。

## 使用Java操作Redis

Java操作Redis使用的是jedis包。

在`pom.xml`添加jedis的依赖，如下图：

![][8]

添加protostuff-core 以及protostuff-runtime 序列化jar包，如下图：

![][9]

序列化是处理对象流的机制，就是将对象的内容进行流化，可以对流化后的对象进行读写操作，也可以将流化后的对象在网络间传输。反序列化就是将流化后的对象重新转化成原来的对象。

在Java中内置了序列化机制，通过implements Serializable来标识一个对象实现了序列化接口，不过其性能并不高。

### 建立操作Redis的dao类

原本查询秒杀商品时是通过主键直接去数据库查询的，选择将数据缓存在Redis，在查询秒杀商品时先去Redis缓存中查询，以此降低数据库的压力。如果在缓存中查询不到数据再去数据库中查询，再将查询到的数据放入Redis缓存中，这样下次就可以直接去缓存中直接查询到。

添加`RedisDao.java`文件，位于下图所示的位置：

![][10]

`RedisDao.java`文件里面的代码请参照项目的源代码。

### 在applicationContext-dao.xml中注入redisDao

在`applicationContext-dao.xml`中添加下图所示的内容：

![][11]

### 改造exportSeckillUrl方法：

修改`SeckillServiceImpl.java`文件中的`exportSeckillUrl`方法：

```java
/**
     * 在秒杀开启时输出秒杀接口的地址，否则输出系统时间跟秒杀地址
     *
     * @param seckillId 秒杀商品Id
     * @return 根据对应的状态返回对应的状态实体
     */
    @Override
    public Exposer exportSeckillUrl(long seckillId) {
        
        Seckill seckill = redisDao.getSeckill(seckillId);
        if (seckill == null) {
            // 访问数据库读取数据
            seckill = seckillMapper.queryById(seckillId);
            if (seckill == null) {
                return new Exposer(false, seckillId);
            } else {
                // 放入redis
                redisDao.putSeckill(seckill);
            }
        }

        // 判断是否还没到秒杀时间或者是过了秒杀时间
        Date startTime = seckill.getStartTime();
        Date endTime = seckill.getEndTime();
        Date nowTime = new Date();
        // 开始时间大于现在的时候说明没有开始秒杀活动；秒杀活动结束时间小于现在的时间说明秒杀已经结束了
        if (nowTime.getTime() > startTime.getTime() && nowTime.getTime() < endTime.getTime()) {
            //秒杀开启,返回秒杀商品的id,用给接口加密的md5
            String md5 = getMd5(seckillId);
            return new Exposer(true, md5, seckillId);
        }
        return new Exposer(false, seckillId, nowTime.getTime(), startTime.getTime(), endTime.getTime());
        
    }
```

## 简单的优化

以前的实现流程：

![][12]

用户的秒杀操作分为两步：减库存、插入购买明细，我们在这里进行简单的优化，就是将原本先update（减库存）再进行insert（插入购买明细）的步骤改成：先insert再update。

![][13]

### 为什么要先insert再update

* 首先是在更新操作的时候给行加锁，插入并不会加锁，如果更新操作在前，那么就需要执行完更新和插入以后事务提交或回滚才释放锁。而如果插入在前，更新在后，那么只有在更新时才会加行锁，之后在更新完以后事务提交或回滚释放锁。

* 在这里，插入是可以并行的，而更新由于会加行级锁是串行的。

* 也就是说是如果更新在前，加锁和释放锁之间两次的网络延迟和GC，如果更新在后，则加锁和释放锁之间只有一次的网络延迟和GC，也就是减少的持有锁的时间。

* 这里先insert并不是忽略了库存不足的情况，而是因为insert和update是在同一个事务里，光是insert并不一定会提交，只有在update成功才会提交，所以并不会造成过量插入秒杀成功记录。

**去代码里改造执行秒杀的`executeSeckill()`方法，优化性能。**

## 深度优化（使用存储过程）

前边通过调整insert和update的执行顺序来实现简单的优化，但依然存在着Java客户端和服务器通信时的网络延迟和GC影响，我们可以将执行秒杀操作时的insert和update放到MySQL服务端的存储过程里，而Java客户端直接调用这个存储过程，这样就可以避免网络延迟和可能发生的GC影响。另外，由于我们使用了存储过程，也就用不到Spring的事务管理了，因为在存储过程里我们会直接启用一个事务。

### 去MySQL的控制台执行储存过程`procedure.sql`里面的代码

**去MySQL的控制台执行储存过程`procedure.sql`里面的代码。**

`procedure.sql`文件位于项目的sql目录下。

注意点：在存储过程中，row_count() 函数用来返回上一条 sql（delete, insert, update）影响的行数。

根据row_count() 的返回值，可以进行接下来的流程判断：

`0`：未修改数据；

`>0`：表示修改的行数；

`<0`：表示SQL错误或未执行修改SQL

## 修改源码以调用存储过程

在`SeckillMapper.java`接口中声明`killByProcedure()`方法

```java
    /**
     * 使用储存过程执行秒杀
     *
     * @param paramMap
     */
    void killByProcedure(Map<String, Object> paramMap);
```

然后在`SeckillMapper.xml`中写`sql`语句，具体代码请参照项目的源代码。

接着在`SeckillService.java`接口中声明 `executeSeckillProcedure()`方法

在pom.xml中添加`commons-collections`的依赖，如下图：

![][14]

然后在`SeckillServiceImpl.java`中实现`executeSeckillProcedure()`方法。

在`SeckillServiceImplTest.java`中编写测试方法`executeSeckillProcedureTest()`。

测试结果：

![][15]

修改`SeckillController.java`中的`execute()`方法，把一开始调用普通方法的改成调用储存过程的方法。

## 存储过程优化总结

* 存储过程优化：事务行级锁持有的时间

* 不要过度依赖存储过程

* 简单的逻辑依赖存储过程

* QPS:一个秒杀单6000/qps

经过简单优化和深度优化之后，本项目大概能达到一个秒杀单6000qps，这个数据对于一个秒杀商品来说其实已经挺ok了，注意这里是指同一个秒杀商品6000qps，如果是不同商品不存在行级锁竞争的问题。

### 系统部署架构

![][16]

CDN：放置一些静态化资源，或者可以将动态数据分离。一些js依赖直接用公网的CDN，自己开发的一些页面也做静态化处理推送到CDN。用户在CDN获取到的数据不需要再访问我们的服务器，动静态分离可以降低服务器请求量。比如秒杀详情页，做成HTML放在CDN上，动态数据可以通过ajax请求后台获取。

Nginx：作为http服务器，响应客户请求，为后端的servlet容器做反向代理，以达到负载均衡的效果。

Redis：用来做服务器端的缓存，通过Jedis提供的API来达到热点数据的一个快速存取的过程，减少数据库的请求量。

MySQL：保证秒杀过程的数据一致性与完整性。

智能DNS解析+智能CDN加速+Nginx并发+Redis缓存+MySQL分库分表，如下图：

![][17]

大型系统部署架构，逻辑集群就是开发的部分。

* Nginx做负载均衡

* 分库分表：在秒杀系统中，一般通过关键的秒杀商品id取模进行分库分表，以512为一张表，1024为一张表。分库分表一般采用开源架构，如阿里巴巴的tddl分库分表框架。

* 统计分析：一般使用hadoop等架构进行分析

在这样一个架构中，可能参与的角色如下：

![][18]

到此，该项目已经全部完成，感谢阅读本文。


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517334205021.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517334438454.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517334600302.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517334903423.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517335304628.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517335653112.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517335681904.jpg
  [8]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517325699734.jpg
  [9]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517325829466.jpg
  [10]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517325955175.jpg
  [11]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517326483818.jpg
  [12]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517337117090.jpg
  [13]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517337228818.jpg
  [14]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517328581037.jpg
  [15]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517330110383.jpg
  [16]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517339924527.jpg
  [17]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517340069294.jpg
  [18]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/31/1517340432690.jpg