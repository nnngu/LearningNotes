# 02 使用Mybatis的逆向工程自动生成代码

## 1、逆向工程的作用

Mybatis 官方提供了逆向工程，可以针对数据库表自动生成Mybatis执行所需要的代码（包括mapper.xml、Mapper.java、pojo）。

## 2、逆向工程的使用方法

逆向工程需要的jar包如下图所示：

![][1]

也可以直接下载我Github上面的源代码（[https://github.com/nnngu/generatorSqlmapCustom](https://github.com/nnngu/generatorSqlmapCustom) ），在 lib 目录下已经添加了需要的 jar 包。

下载下来的项目目录如下图：

![][2]

从上图中看，①是依赖的jar包。②是配置文件。③是要执行的Java代码，执行它即可生成我们需要的代码。

### 2-1、先把配置文件写好

`generatorConfig.xml`的代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/taotao_0303" userId="root"
                        password="1">
        </jdbcConnection>
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.taotao.pojo"
                            targetProject="./src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.taotao.mapper"
                         targetProject="./src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.taotao.mapper"
                             targetProject="./src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>
        <!-- 指定数据库表 -->
        <table schema="" tableName="tb_content"></table>
        <table schema="" tableName="tb_content_category"></table>
        <table schema="" tableName="tb_item"></table>
        <table schema="" tableName="tb_item_cat"></table>
        <table schema="" tableName="tb_item_desc"></table>
        <table schema="" tableName="tb_item_param"></table>
        <table schema="" tableName="tb_item_param_item"></table>
        <table schema="" tableName="tb_order"></table>
        <table schema="" tableName="tb_order_item"></table>
        <table schema="" tableName="tb_order_shipping"></table>
        <table schema="" tableName="tb_user"></table>

    </context>
</generatorConfiguration>
```

从上面的配置文件中可以看出，配置文件主要做了几件事：

1、连接数据库，这是必须的，要不然怎么根据数据库的表生成代码呢？

2、指定要生成代码的位置，要生成的代码包括 pojo类、映射文件mapper.xml、接口Mapper.java。注意：指定生成代码的位置时，目录分隔符：window系统使用`\`，Linux和Mac系统使用`/`。

3、指定数据库中想要生成哪些表

### 2-2、执行逆向工程，生成代码

配置文件写好了，然后执行`GeneratorSqlmap.java` 里面的main方法，即可自动生成代码。

`GeneratorSqlmap.java`的代码如下：

```java
import java.io.File;
import java.util.ArrayList;
import java.util.List;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.exception.XMLParserException;
import org.mybatis.generator.internal.DefaultShellCallback;

public class GeneratorSqlmap {

    public void generator() throws Exception {

        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        //指定 逆向工程配置文件
        File configFile = new File("generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                callback, warnings);
        myBatisGenerator.generate(null);

    }

    public static void main(String[] args) throws Exception {
        try {
            GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
            generatorSqlmap.generator();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}

```

运行一下即可，然后在src目录下就可以看到最新生成的代码了，如下图：

![][3]

大功告成！把这些自动生成的代码复制到我们真正的项目中即可。














---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Mybatis/02%20%E4%BD%BF%E7%94%A8Mybatis%E7%9A%84%E9%80%86%E5%90%91%E5%B7%A5%E7%A8%8B%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E4%BB%A3%E7%A0%81.md](https://github.com/nnngu/LearningNotes/blob/master/Mybatis/02%20%E4%BD%BF%E7%94%A8Mybatis%E7%9A%84%E9%80%86%E5%90%91%E5%B7%A5%E7%A8%8B%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E4%BB%A3%E7%A0%81.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/5/1520263736178.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/5/1520264307661.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/6/1520265701449.jpg