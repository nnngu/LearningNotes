# 01 Spring的简单配置和使用

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

## Spring需要的jar包

文章中 Spring 使用的版本是 `3.2.18` ， 需要的 jar 包如下：

```
spring-aop
spring-aspects
spring-beans
spring-context
spring-context-support
spring-core
spring-expression
spring-instrument
spring-instrument-tomcat
spring-jdbc
spring-jms
spring-messaging 4.3.14
spring-orm
spring-oxm
spring-test
spring-tx
spring-web
spring-webmvc
spring-webmvc-portlet
spring-websocket 4.3.14
aopalliance 1.0
aspectjweaver 1.8.13
cglib 3.1
commons-collections 3.2.2
commons-dbcp 1.4
commons-logging 1.1.1
commons-pool 1.6
standard 1.1.2
```

使用 Maven 构建的 Java 项目，需要在 pom.xml 中添加如下依赖：

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-instrument</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-instrument-tomcat</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jms</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-messaging</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-oxm</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>3.2.18.RELEASE</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc-portlet</artifactId>
            <version>3.2.18.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-websocket</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
        <dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
            <version>3.1</version>
        </dependency>
        <dependency>
            <groupId>commons-collections</groupId>
            <artifactId>commons-collections</artifactId>
            <version>3.2.2</version>
        </dependency>
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.4</version>
        </dependency>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.1.1</version>
        </dependency>
        <dependency>
            <groupId>commons-pool</groupId>
            <artifactId>commons-pool</artifactId>
            <version>1.6</version>
        </dependency>
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
```

## 新建一个 HelloWorld.java

新建一个`HelloWorld.java`，代码如下：

```java
package com.nnngu;

public class HelloWorld {
    private String info;

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }
}

```

## 编写配置文件 applicationContext.xml

在 resources 目录下创建 applicationContext.xml 配置文件

![][1]

代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <!-- 配置需要被Spring管理的Bean（创建后放在了Spring IOC容器里面）-->
    <bean id="hello" class="com.nnngu.HelloWorld">
        <!-- 配置该Bean需要注入的属性（是通过属性set方法来注入的） -->
        <property name="info" value="2018，Happy New Year!"/>
    </bean>

</beans>
```

## 使用

创建 `Main.java` ，代码如下：

```java
package com.nnngu;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

    public static void main(String[] args) {
        // 获取Spring的applicationContext配置文件，注入IOC容器中
        BeanFactory factory = new ClassPathXmlApplicationContext("applicationContext.xml");
        HelloWorld hw = (HelloWorld)factory.getBean("hello");
        System.out.println(hw.getInfo());
        System.out.println(hw);
    }
}

```

运行结果：

![][2]


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/12/1518417902156.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/12/1518418286415.jpg