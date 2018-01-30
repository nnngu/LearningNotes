# 04 Java高并发秒杀项目之高并发优化
Github：[https://github.com/nnngu](https://github.com/nnngu)   
项目源代码：[https://github.com/nnngu/nguSeckill](https://github.com/nnngu/nguSeckill)  

---

## 下载安装Redis

Redis是一个开源的、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库

下载安装Redis的步骤，搜索引擎能找到相关的资料，本文不做展开。

下载安装完Redis之后就可以继续进行操作。

## 使用Java操作Redis

Java操作Redis使用的是jedis包。

在`pom.xml`添加jedis的依赖，如下图：

![][1]

添加protostuff-core 以及protostuff-runtime 序列化jar包，如下图：

![][2]

`pom.xml`文件里面的完整代码请参照项目的源代码。

### 建立操作Redis的dao类

添加`RedisDao.java`文件，位于下图所示的位置：

![][3]

`RedisDao.java`文件里面的代码请参照项目的源代码。

### 在applicationContext-dao.xml中注入redisDao

在`applicationContext-dao.xml`中添加下图所示的内容：

![][4]

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

### 写存储过程

去Mysql控制台执行储存过程






  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517325699734.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517325829466.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517325955175.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/30/1517326483818.jpg