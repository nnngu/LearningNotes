# 01 Mybatis 的配置和使用

## 一、Mybatis 是什么

![][1]

MyBatis 是一个支持普通SQL查询、存储过程和高级映射的优秀持久层框架。MyBatis 消除了几乎所有的 JDBC 代码和参数的手工设置以及对结果集的检索封装。MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录。

## 二、Mybatis 的使用

### 1、导包

Mybatis 需要以下的 jar 包：

```
mybatis.jar
mysql-connector-java.jar
```

使用 Maven 构建的项目，需要在 pom.xml 中添加如下依赖：

```xml
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.2.7</version>
        </dependency>

        <!--数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
```

### 2、建表

创建数据库和表，针对MySQL数据库，SQL脚本如下：

```sql
create database mybatis_demo;
use mybatis_demo;
CREATE TABLE users(id INT PRIMARY KEY AUTO_INCREMENT, NAME VARCHAR(20), age INT);
insert into users values(null,'郭靖', 27);
insert into users values(null,'黄蓉', 17);
```

![][2]

![][3]

到此，建表工作已经完成。

### 3、创建表所对应的实体类

如下图所示：

![][4]

User 类的代码如下：

```java
package com.nnngu.domain;

public class User {
    // 实体类的属性和表的字段名称一一对应
    private int id;
    private String name;
    private int age;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User [id=" + id + ", name=" + name + ", age=" + age + "]";
    }
}

```

### 4、创建用来操作 users 表的 sql 映射文件 userMapper.xml

![][5]

`userMapper.xml` 的代码如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 为这个mapper指定一个唯一的namespace，namespace的值习惯上设置成包名+sql映射文件名，这样就能够保证namespace的值是唯一的 -->
<mapper namespace="com.nnngu.mapping.userMapper">

    <!-- 在select标签中编写查询的SQL语句， select标签的id属性为getUser，id属性值必须是唯一的，不能够重复
    使用parameterType属性指明查询时使用的参数类型，resultType属性指明查询返回的结果集类型
    resultType="com.nnngu.domain.User"就表示将查询结果封装成一个User类的对象返回
    User类就是users表所对应的实体类
    -->
    <select id="getUser" parameterType="int"
            resultType="com.nnngu.domain.User">
        select * from users where id=#{id}
    </select>
</mapper>
```

### 5、创建 Mybatis 的配置文件

创建配置文件 `mybatis_config.xml` ，在下图所示的位置

![][6]

`mybatis_config.xml`的代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!-- 配置数据库连接信息 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis_demo"/>
                <property name="username" value="root"/>
                <property name="password" value="1"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 注册userMapper.xml -->
    <mappers>
        <mapper resource="com.nnngu.mapping/userMapper.xml"/>
    </mappers>

</configuration>
```

### 6、测试

创建一个Test1类，编写如下的测试代码：

```java
package com.nnngu.test;

import com.nnngu.domain.User;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class Test1 {
    public static void main(String[] args) throws IOException {
        // mybatis 的配置文件
        String resource = "mybatis_config.xml";

        // 使用类加载器加载mybatis的配置文件（它也加载关联的映射文件）
        InputStream is = Test1.class.getClassLoader().getResourceAsStream(resource);

        // 构建sqlSession的工厂
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(is);

        // 创建能执行映射文件中sql的sqlSession
        SqlSession session = sessionFactory.openSession();

        /**
         * 映射sql的标识字符串，
         * com.nnngu.mapping.userMapper是userMapper.xml文件中mapper标签的namespace属性的值，
         * getUser是select标签的id属性值，通过select标签的id属性值就可以找到要执行的SQL
         */
        String statement = "com.nnngu.mapping.userMapper.getUser"; // 映射sql的标识字符串

        // 执行查询返回一个user对象的sql
        User user = session.selectOne(statement, 1);
        System.out.println(user);
    }
}

```

测试结果：

![][7]















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Mybatis/01%20Mybatis%20%E7%9A%84%E9%85%8D%E7%BD%AE%E5%92%8C%E4%BD%BF%E7%94%A8.md](https://github.com/nnngu/LearningNotes/blob/master/Mybatis/01%20Mybatis%20%E7%9A%84%E9%85%8D%E7%BD%AE%E5%92%8C%E4%BD%BF%E7%94%A8.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519295758420.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519296834079.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519296882222.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519297821232.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519298235672.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519297480387.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/22/1519299368518.jpg