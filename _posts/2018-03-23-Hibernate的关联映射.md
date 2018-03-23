---
categories: Hibernate
description: 摘要：让我们一起走进Hibernate的几种关联映射关系。
---

首先我们了解一个名词ORM，全称是（Object Relational Mapping），即对象关系映射。ORM的实现思想就是将关系型数据库中表的数据映射成对象，以对象的形式展现，这样开发人员就可以把对数据库的操作转化为对这些对象的操作。Hibernate正是实现了这种思想，达到了方便开发人员以面向对象的思想来实现对数据库的操作。

Hibernate在实现ORM功能的时候主要用到的文件有：映射类`（*.java）`、映射文件`（*.hbm.xml）`和数据库配置文件`（*.properties/*.cfg.xml）`，它们各自的作用如下：

* 映射类`（*.java）`：它是描述数据库表的结构，表中的字段在类中被描述成属性，将来就可以实现把表中的记录映射成为该类的对象了。
* 映射文件`（*.hbm.xml）`：它是指定数据库表和映射类之间的关系，包括映射类和数据库表的对应关系、表字段和类属性的对应关系。
* 数据库配置文件`（*.properties/*.cfg.xml）`：它是指定与数据库连接时需要的连接信息，比如连接哪种数据库、登录数据库的用户名、密码以及连接字符串等。当然还可以把映射类的地址映射信息放在这里。

## 接下来让我们一起走进Hibernate的几种关联映射关系：

### 单向一对一关联映射（one-to-one）：

两个对象之间一对的关系，例如：Person（人）-  IdCard（身份证）

有两种策略可以实现一对一的关联映射：

* 主键关联：即让两个对象具有相同的主键值，以表明它们之间的一一对应的关系；数据库表不会有额外的字段来维护它们之间的关系，仅通过表的主键来关联。如下图：

![][1]

注意：需要在Person.hbm.xml映射文件中配置`one-to-one`标签，如下：

```xml
<?xml version="1.0"?>  
<!DOCTYPE hibernate-mapping PUBLIC   
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  
    "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">  
<hibernate-mapping>  
    <class name="com.nnngu.Person" table="t_person">  
        <id name="id">  
            <!-- 采用foreign生成策略，foreign会取得关联对象的标识 -->  
            <generator class="foreign">  
                <!-- property只关联对象 -->  
                <param name="property">idCard</param>  
            </generator>  
        </id>  
        <property name="name"/>  
        <!-- one-to-one指示hibernate如何加载其关联对象，默认根据主键加载  
            也就是拿到关系字段值，根据对端的主键来加载关联对象  
         -->  
        <one-to-one name="idCard" constrained="true"/>  
    </class>  
</hibernate-mapping>  
```

* 唯一外键关联：外键关联，本来是用于多对一的配置，但是加上唯一的限制之后（采用`<many-to-one>`标签来映射，指定多的一端unique为true，这样就限制了多的一端的多重性为一），也可以用来表示一对一关联关系，其实它就是多对一的特殊情况。如下图：

![][2]

需要在Person.hbm.xml映射文件中配置`many-to-one`标签，并指定unique为true，如下：

```xml
<?xml version="1.0"?>  
<!DOCTYPE hibernate-mapping PUBLIC   
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  
    "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">  
<hibernate-mapping>  
    <class name="com.nnngu.Person" table="t_person">  
        <id name="id">  
            <generator class="native">  
                <!-- property只关联对象 -->  
                <param name="property">idCard</param>  
            </generator>  
        </id>  
        <property name="name"/>  
        <many-to-one name="idCard" unique="true"/>  
    </class>  
</hibernate-mapping> 
```

**注意：因为一对一的主键关联映射扩展性不好，当我们的需要发生改变想要将其变为一对多的时候变无法操作了，所以我们遇到一对一关联的时候经常会采用唯一外键关联来解决问题，而很少使用一对一主键关联。**

### 单向多对一关联映射（many-to-one）：

多对一关联映射原理：在多的一端加入一个外键，指向一的一端，如下图：

![][3]

关键映射代码——在多的一端加入如下标签映射：

```xml
<many-to-one name="group" column="groupid"/>  
```

### 单向一对多关联映射（one-to-many）：

一对多关联映射和多对一关联映射原理是一致的，都是在多的一端加入一个外键，指向一的一端。如下图（学生和班级）：

![][4]

注意：它与多对一的区别是维护的关系不同

* 多对一维护的关系是：多指向一的关系，有了此关系，加载多的时候可以将一加载上来。
* 一对多维护的关系是：一指向多的关系，有了此关系，在加载一的时候可以将多加载上来。

关键映射代码——在一的一端加入如下标签映射：

```xml
<set name="students">  
      <key column="classesid"/>  
      <one-to-many class="com.nnngu.Student"/>  
</set> 
```

### 单向多对多映射（many-to-many）：

多对多关联映射需要新增加一张表才完成基本映射，如下图：

![][5]

关键映射代码——可以在User的一端加入如下标签映射：

```xml
<set name="roles" table="t_user_role">  
     <key column="user_id"/>  
     <many-to-many class="com.nnngu.Role" column="role_id"/>  
</set>
```

### 双向一对一关联映射：

对比单向一对一映射，需要在IdCard也加入`<one-to-one>`标签。示意图如下：

![][6]

双向一对一主键映射关键映射代码——在IdCard端新加入如下标签映射：

```xml
<one-to-one name="person"/> 
```

双向一对一唯一外键映射关键映射代码——在IdCard端新加入如下标签映射：

```xml
<one-to-one name="person"property-ref="idCard"/>  
```

注意：一对一唯一外键关联双向采用`<one-to-one>`标签映射，必须指定`<one-to-one>`标签中的property-ref属性为关系字段的名称

### 双向一对多关联映射（非常重要）：

采用双向一对多关联映射的目的主要是为了解决单向一对多关联的缺陷。

双向一对多关联的映射方式：

* 在一的一端的集合上采用`<key>`标签，在多的一端加入一个外键
* 在多的一端采用`<many-to-one>`标签

注意：`<key>`标签和`<many-to-one>`标签加入的字段要保持一致，否则会产生数据混乱。

在Classes的一端加入如下标签映射：     

```xml
<set name="students"inverse="true">  
      <key column="classesid"/>  
      <one-to-many class="com.nnngu.Student"/>  
</set> 
```

在Student的一端加入如下标签映射：

```xml
<many-to-one name="classes" column="classesid"/>  
```

>  了解inverse属性：
> * inverse属性可以用在一对多和多对多双向关联上，inverse属性默认为false，为false表示本端可以维护关系，如果inverse为true，则本端不能维护关系，会交给另一端维护关系，本端失效。所以一对多关联映射我们通常在多的一端维护关系，让一的一端失效。
> * inverse是控制方向上的反转，只影响存储。

### 双向多对多关联映射：

双向的目的就是为了两端都能将对方加载上来，和单向多对多的区别就是双向需要在两端都加入标签映射，需要注意的是：

* 生成的中间表名称必须一样
* 生成的中间表中的字段必须一样

Role（角色）端关键映射代码： 

```xml
<set name="users" table="t_user_role">  
       <key column="role_id"/>  
       <many-to-many class="com.nnngu.User" column="user_id"/>  
</set>  
```

User（用户）端关键映射代码：

```xml
<set name="roles" table="t_user_role">  
      <key column="user_id"/>  
      <many-to-many class="com. nnngu.Role" column="role_id"/>  
</set> 
```

## 总结

对于上面的七种关联映射，最重要的就是一对多的映射，因为它更贴近我们的现实生活，比如：教室和学生就可以是典型的一对多的关系，而我们开发软件的目的之一就是为了解决一些生活中重复性问题，把那些重复的问题交给计算机来帮助我们完成，从而提高我们的工作效率。
















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-23-Hibernate%E7%9A%84%E5%85%B3%E8%81%94%E6%98%A0%E5%B0%84.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-23-Hibernate%E7%9A%84%E5%85%B3%E8%81%94%E6%98%A0%E5%B0%84.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521802267620.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521802632681.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521803063292.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521803178710.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521803421510.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521803600888.jpg