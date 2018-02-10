# 01 Struts2的配置和一个简单的例子

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

## 简介

这篇文章主要讲如何在 IntelliJ IDEA 中使用 Struts2，文章使用的  Struts2 的版本是2.5.14.1，与其他的版本有一点差别，在文章里已经说明。

## 环境

IntelliJ IDEA 2017.2.6

jdk1.8.0_101

Tomcat 8.0.38

## 添加依赖

依赖的 jar 包有如下几个：

```
commons-fileupload-1.3.3.jar
commons-io-2.5.jar
commons-lang3-3.6.jar
freemarker-2.3.26.jar
log4j-api-2.9.1.jar
ognl-3.1.15.jar
struts2-core-2.5.14.1.jar
javassist-3.20.0-GA.jar
```

使用 Maven 构建的项目，需要在 pom.xml 添加如下依赖：

```xml
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.3</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.6</version>
        </dependency>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.26-incubating</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>2.9.1</version>
        </dependency>
        <dependency>
            <groupId>ognl</groupId>
            <artifactId>ognl</artifactId>
            <version>3.1.15</version>
        </dependency>
        <dependency>
            <groupId>org.apache.struts</groupId>
            <artifactId>struts2-core</artifactId>
            <version>2.5.14.1</version>
        </dependency>
        <dependency>
            <groupId>org.javassist</groupId>
            <artifactId>javassist</artifactId>
            <version>3.20.0-GA</version>
        </dependency>
```




