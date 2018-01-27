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

        <!-- 省略... -->
		... ...
        
```
完整的代码，请查看项目里的`pom.xml`文件

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

## 修改`web.xml`

打开`WEB-INF`下的`web.xml`，修改为以下代码:

```xml

```


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