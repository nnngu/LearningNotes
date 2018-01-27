# 01 Java高并发秒杀项目之业务分析与DAO层
作者：[nnngu](https://github.com/nnngu)  

---

这是一个整合IDEA+Maven+SSM框架的高并发的商品秒杀项目。我们将分为以下几篇文章来进行详细的讲解：

* 01 Java高并发秒杀项目之业务分析与DAO层
* 02 Java高并发秒杀项目之web层
* 03 Java高并发秒杀项目之Service层
* 04 Java高并发秒杀项目之高并发优化

## 项目的效果图

### 秒杀商品列表
![秒杀商品列表][1]

### 开始秒杀提示界面
![开始秒杀提示界面][2]

### 秒杀结束提示界面
![秒杀结束提示界面][3]

## 新建一个Maven项目

以IntelliJ IDEA为例，点击左上角`File > New > Project > Maven`

![然后点击Next继续；][4]
然后点击Next继续；


![填写相关信息，点击Next；][5]
填写相关信息，点击Next；


![最后点击Finish，完成创建。][6]
最后点击Finish，完成创建。


如果右下角弹出下图这个提示，点击`Enable Auto-Import`

![如果弹出这个提示，点击Enable Auto-Import][7]

## 构建pom文件

Maven项目创建好了，接下来我们要添加一些基本的JAR包的依赖，也就是在`pom.xml`中添加各种开源组件的坐标。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.nnngu</groupId>
    <artifactId>nguSeckill</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>

        <!--junit测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!--配置日志相关,日志门面使用slf4j,日志的具体实现由logback实现-->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.1.7</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.21</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.6.1</version>
        </dependency>

        <!--数据库相关依赖-->
        <!--首先导入连接Mysql数据连接-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.39</version>
        </dependency>

        <!--导入数据库连接池-->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <!--导入mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.2</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!--导入Servlet web相关的依赖-->
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <!--spring默认的json转换-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>

        <!--导入spring相关依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.3.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.3.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.3.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.3.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!--导入springTest-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.2.7.RELEASE</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>seckill</finalName>
    </build>

</project>
```












  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517020356103.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517020518954.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517020478158.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517021726058.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517021904910.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517022082406.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/27/1517022266134.jpg