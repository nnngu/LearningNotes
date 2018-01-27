# 02 Java高并发秒杀项目之Service层
作者：[nnngu](https://github.com/nnngu)  

---

首先在编写`Service`层代码前，我们应该首先要知道这一层到底是干什么的。

`Service`层主要负责业务模块的逻辑应用设计。同样是首先设计接口，再设计其实现的类，接着在`Spring`的配置文件中配置其实现的关联。这样我们就可以在应用中调用`Service`接口来进行业务处理。`Service`层的业务实现，具体要调用到已定义的`dao`层的接口，封装`Service`层的业务逻辑有利于通用的业务逻辑的独立性和重复利用性，程序显得非常简洁。

在项目中要降低耦合的话，分层是一种很好的概念，就是各层各司其职，尽量不做不相干的事，所以`Service`层的话顾名思义就是业务逻辑，处理程序中的一些业务逻辑，以及调用`dao`层的代码，这里我们的`dao`层就是连接数据库的那一层，调用关系可以这样表达：

View(页面) > Controller(控制层) > Service(业务逻辑) > Dao(数据访问) > Database(数据库)

首先还是接口的设计，设计秒杀商品的接口，首先在`com.nnngu`包下建立 `interfaces` 这个包，这个包里面存放 `Service` 相关的接口，然后建立`SeckillService`接口文件，如下图：

![][1]

`SeckillService.java`文件里面的内容请参照项目的源代码。

建立好接口之后我们要写实现类了，在写实现类的时候我们肯定会碰到一个这样的问题，你要向前端返回json数据的话，你是返回什么样的数据好？直接返回一个数字状态码或者文字？这样设计肯定是不好的，所以我们应该向前端返回一个实体信息json，里面包含了一系列的信息，无论是哪种状态都应该可以应对，既然是与数据库字段无关的类，那就不是PO了，所以我们建立一个DTO数据传输类。关于常见的几种对象我的解释如下：

* PO：也就是我们为每一张数据库表写一个实体类

* VO：对某个页面或者展现层所需要的数据，封装成一个实体类

* BO：业务对象

* DTO：跟VO的概念有点混淆，也是相当于页面需要的数据封装成一个实体类

* POJO：简单的无规则java对象

在com.nnngu下建立dto包，然后建立Exposer类，这个类是秒杀时数据库那边处理的结果的对象

`Exposer.java`文件里面的内容请参照项目的源代码。

## 定义秒杀中可能会出现的异常

定义一个基础的异常，所有的子异常继承这个异常`SeckillException`

```java
package com.nnngu.exception;

/**
 *  秒杀基础的异常
 * Created by nnngu
 */
public class SeckillException extends RuntimeException {
    // 代码省略，请参照项目的源代码
	... ...
}

```

可能会出现秒杀关闭后被秒杀情况，所以建立秒杀关闭异常`SeckillCloseException`，需要继承我们前面写的基础异常

```java
package com.nnngu.exception;

/**
 * 秒杀已经关闭异常，当秒杀结束就会出现这个异常
 * Created by nnngu
 */
public class SeckillCloseException extends SeckillException{
    // 代码省略，请参照项目的源代码
	... ...
}

```

定义重复秒杀异常`RepeatKillException`

```java
package com.nnngu.exception;

/**
 * 重复秒杀异常，不需要我们手动去try catch
 * Created by nnngu
 */
public class RepeatKillException extends SeckillException{
    // 代码省略，请参照项目的源代码
	... ...
}

```

## 实现`Service`接口

`com.nnngu.service`包下创建`SeckillServiceImpl.java`类，具体代码请参照项目的源代码。

在这里我们捕获了运行时异常，这样做的原因就是Spring的事务默认发生了RuntimeException才会回滚，可以检测出来的异常是不会导致事务的回滚的，这样的目的就是你明知道这里会发生异常，所以你一定要进行处理。如果只是为了让编译通过的话，那捕获异常也没意思，所以这里要注意事务的回滚。

然后我们还发现这里存在硬编码的现象，就是返回各种字符常量，例如秒杀成功，秒杀失败等等，这些字符串是可以被重复使用的，而且这样维护起来也不方便，要到处去类里面寻找这样的字符串，所有我们使用枚举类来管理这样状态，在con.nnngu包下建立enums包，专门放置枚举类，然后再建立SeckillStatEnum枚举类。

枚举类`SeckillStatEnum.java`的代码请参照项目的源代码。

## 注入Service

在`resources/spring`下建立`applicationContext-service.xml`文件,用来配置`Service`层

`applicationContext-service.xml`的代码请参照项目的源代码。

在这里开启了基于注解的事务，常见的事务操作有以下几种方法：

* 在Spring早期版本中是使用ProxyFactoryBean+XMl方式来配置事务。
* 在Spring配置文件使用tx:advice+aop命名空间，好处就是一次配置永久生效，你无须去关心中间出的问题，不过出错了你很难找出在哪里出了问题。
* 注解@Transactional的方式，注解可以在方法定义，接口定义，类定义。可以在public方法上，但是不能注解在private、final、static等方法上，因为Spring的事务管理默认是使用cglib动态代理的：
  * private方法因为访问权限限制，无法被子类覆盖
  * final方法无法被子类覆盖
  * static时类级别的方法，无法被子类覆盖
  * protected方法可以被子类覆盖，因此可以被动态字节码增强









  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517087378325.jpg