---
categories: Hibernate
description: 摘要：让我们一起走进Hibernate的几种关联映射关系。
---

首先我们了解一个名词ORM，全称是（Object Relational Mapping），即对象关系映射。ORM的实现思想就是将关系型数据库中表的数据映射成对象，以对象的形式展现，这样开发人员就可以把对数据库的操作转化为对这些对象的操作。Hibernate正是实现了这种思想，达到了方便开发人员以面向对象的思想来实现对数据库的操作。

Hibernate在实现ORM功能的时候主要用到的文件有：映射类`（*.java）`、映射文件`（*.hbm.xml）`和数据库配置文件`（*.properties/*.cfg.xml）`，它们各自的作用如下：

* 映射类`（*.java）`：它是描述数据库表的结构，表中的字段在类中被描述成属性，将来就可以实现把表中的记录映射成为该类的对象了。
* 映射文件`（*.hbm.xml）`：它是指定数据库表和映射类之间的关系，包括映射类和数据库表的对应关系、表字段和类属性类型的对应关系以及表字段和类属性名称的对应关系等。
* 数据库配置文件`（*.properties/*.cfg.xml）`：它是指定与数据库连接时需要的连接信息，比如连接哪种数据库、登录数据库的用户名、登录密码以及连接字符串等。当然还可以把映射类的地址映射信息放在这里。



















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-23-Hibernate%E7%9A%84%E5%85%B3%E8%81%94%E6%98%A0%E5%B0%84.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-23-Hibernate%E7%9A%84%E5%85%B3%E8%81%94%E6%98%A0%E5%B0%84.md)