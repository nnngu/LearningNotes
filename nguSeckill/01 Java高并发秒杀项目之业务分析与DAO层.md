# 01 Java高并发秒杀项目之业务分析与DAO层
作者：[nnngu](https://github.com/nnngu)  
项目源代码：https://github.com/nnngu/nguSeckill

---

这是一个整合IDEA+Maven+SSM框架的高并发的商品秒杀项目。我们将分为以下几篇文章来进行详细的讲解：

* 01 Java高并发秒杀项目之业务分析与DAO层
* 02 Java高并发秒杀项目之Service层
* 03 Java高并发秒杀项目之web层
* 04 Java高并发秒杀项目之高并发优化

## 项目的效果图

### 秒杀商品列表
![秒杀商品列表][1]

### 开始秒杀提示界面
![开始秒杀提示界面][2]

### 秒杀结束提示界面
![秒杀结束提示界面][3]

## 新建一个Maven项目

以IntelliJ IDEA为例，点击`File > New > Project > Maven`

![然后点击Next继续；][4]

然后点击Next继续；


![填写相关信息，点击Next；][5]

填写相关信息，点击Next；


![最后点击Finish，完成创建。][6]

最后点击Finish，完成创建。


如果右下角弹出下面这个提示，点击`Enable Auto-Import`

![如果弹出这个提示，点击Enable Auto-Import][7]

## 创建webapp目录

点击`File > Project Structure`

步骤1

![][8]

步骤2

![][9]

步骤3

![][10]

步骤4

![][11]

步骤5

![][12]

步骤6

![][13]

步骤7

![][14]

步骤8

![][15]


## 构建pom文件

Maven项目创建好了，接下来我们要添加一些jar包的依赖，也就是在`pom.xml`中添加各种开源组件的坐标。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.nnngu</groupId>
    <artifactId>nguSeckill</artifactId>
    <version>1.0-SNAPSHOT</version>

        <!-- 代码省略，请参照项目的源代码 -->
		... ...
        
```
完整的代码，请查看项目里的`pom.xml`文件

到此，我们项目的初始化工作完成。

## 秒杀系统业务分析

**秒杀系统业务流程如下：**

![][16]

由图可以发现，整个系统其实是针对库存做的系统。用户成功秒杀商品，对于我们系统的操作就是：

1. 减库存

2. 记录用户的购买明细。下面看看我们用户对库存的业务分析：

![][17]

记录用户的秒杀成功信息，我们需要记录：1、谁购买成功了。2、购买成功的时间/有效期。3、付款/发货信息。这些数据组成了用户的秒杀成功信息，也就是用户的购买行为。

为什么我们的系统需要事务？看如下这些故障：1、若是用户成功秒杀商品我们记录了其购买明细却没有减库存。导致商品的超卖。2、减了库存却没有记录用户的购买明细。导致商品的少卖。对于上述两个故障，若是没有事务的支持，损失最大的无疑是我们的用户和商家。在MySQL中，它内置的事务机制，可以准确的帮我们完成减库存和记录用户购买明细的过程。

MySQL实现秒杀的难点分析：当用户A秒杀id为10的商品时，此时MySQL需要进行的操作是：1、开启事务。2、更新商品的库存信息。3、添加用户的购买明细，包括用户秒杀的商品id以及唯一标识用户身份的信息如电话号码等。4、提交事务。若此时有另一个用户B也在秒杀这件id为10的商品，他就需要等待，等待到用户A成功秒杀到这件商品然后MySQL成功的提交了事务他才能拿到这个id为10的商品的锁从而进行秒杀，而同一时间是不可能只有用户B在等待，肯定是有很多很多的用户都在等待拿到这个行级锁。秒杀的难点就在这里，如何高效的处理这些竞争？如何高效的完成事务？在后面第4个模块如何进行高并发的优化为大家讲解。

我们这个系统需要完成秒杀的哪些功能？先来看看天猫的一个秒杀库存系统：

![][18]

大家看了是不是觉得很复杂？当然不用担心，我们只是实现秒杀的一些功能：1、秒杀接口的暴露。2、执行秒杀的操作。3、相关查询，比如说列表查询，详情页查询。我们实现这三个功能即可。接下来进行具体的编码工作，首先是建立数据库以及Dao层的编码。

## 建立数据库

```sql
-- 创建一个数据库
CREATE DATABASE ngu_seckill;

-- 使用数据库
USE ngu_seckill;

-- 省略...
... ...

```

完整的数据库sql代码，在项目的`sql`文件夹里的`ngu_seckill.sql`

## 创建实体类

先创建秒杀商品类`com/nnngu/entity/Seckill.java`

```java
package com.nnngu.entity;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * 秒杀商品
 */
public class Seckill implements Serializable {

    private static final long serialVersionUID = 2912164127598660137L;
    /* 主键ID*/
    private long seckillId;
    /*  秒杀商品名字 */
    private String name;
	
	/* 代码省略，请参照项目的源代码 */
	... ...
	
```

创建秒杀状态类`com/nnngu/entity/SuccessKilled.java`

```java
package com.nnngu.entity;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * 秒杀后的状态
 */
public class SuccessKilled implements Serializable {
    private static final long serialVersionUID = 1834437127882846202L;

    private long seckillId;
    
    /* 用户的手机号码*/
    private long userPhone;
	
	/* 代码省略，请参照项目的源代码 */
	... ...
	
```

## 为实体类创建对应的mapper接口，也就是dao接口

`com/nnngu/dao/SeckillMapper.java`

```java
package com.nnngu.dao;

import com.nnngu.entity.Seckill;
import org.apache.ibatis.annotations.Param;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Map;

public interface SeckillMapper {
    /* 代码省略，请参照项目的源代码 */
	... ...
	
}
```

`com/nnngu/dao/SuccessKilledMapper.java`

```java
package com.nnngu.dao;

import com.nnngu.entity.SuccessKilled;
import org.apache.ibatis.annotations.Param;

public interface SuccessKilledMapper {
    /* 代码省略，请参照项目的源代码 */
	... ...
	
}

```

## 创建对应的`mapper.xml`

在`resources`目录下创建`com.nnngu.dao`包，然后创建`SeckillMapper.xml` 和 `SuccessKilledMapper.xml`，如下图：

![][19]

`com.nnngu.dao/SeckillMapper.xml`

```xml
<!-- 这里的代码省略 -->
<!-- 请参照项目的源代码 -->
```

`com.nnngu.dao/SuccessKilledMapper.xml`

```xml
<!-- 这里的代码省略 -->
<!-- 请参照项目的源代码 -->
```

## 创建Mybatis的配置文件mybatis-config.xml

![][20]

`mybatis-config.xml`配置文件的内容参照项目的源代码


## 建立连接数据库的配置文件`jdbc.properties`

![][21]

**注意：** `jdbc.properties` 里面的属性要根据自己的情况进行修改。

## 建立Spring的dao的配置文件

**创建`applicationContext-dao.xml`如下图：**

![][22]

`applicationContext-dao.xml`文件的具体代码请参照项目的源代码。

## 测试

创建测试类 `com/nnngu/dao/SeckillMapperTest.java` 如下图：

![][23]

该文件的具体代码请参照项目的源代码。

### 测试结果

测试查询所有商品的方法`queryAll()`

![][24]

测试结果如下：

![][25]

到此，我们成功完成了Dao层开发及测试。下篇文章 [02 Java高并发秒杀项目之Service层](https://github.com/nnngu/LearningNotes/blob/master/nguSeckill/02%20Java%E9%AB%98%E5%B9%B6%E5%8F%91%E7%A7%92%E6%9D%80%E9%A1%B9%E7%9B%AE%E4%B9%8BService%E5%B1%82.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517020356103.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517020518954.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517020478158.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517021726058.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517021904910.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517022082406.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517022266134.jpg
  [8]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517061858789.jpg
  [9]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517062252122.jpg
  [10]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517061346663.jpg
  [11]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517061459422.jpg
  [12]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517061601898.jpg
  [13]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517064377716.jpg
  [14]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517064456606.jpg
  [15]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517065310557.jpg
  [16]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517078418982.jpg
  [17]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517078595990.jpg
  [18]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517078980637.jpg
  [19]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517069800569.jpg
  [20]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517070510826.jpg
  [21]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517071709680.jpg
  [22]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517072120210.jpg
  [23]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517076548761.jpg
  [24]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517076812755.jpg
  [25]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/28/1517077063824.jpg