---
categories: Hibernate
description: 摘要：本文主要总结继承映射的三种实现方式。
---

## 对象模型示例：

![][1]

## 继承映射的实现方式有以下三种：

* （一）每棵类继承树一张表
* （二）每个类一张表
* （三）每个子类一张表

### （一）每棵类继承树一张表

关系模型如下：

![][2]

映射文件如下：

```xml
<hibernate-mapping package="com.nnngu">  
    <class name="Animal" table="t_animal" lazy="false">  
        <id name="id">  
            <generator class="native"/>  
        </id>  
        <discriminator column="type" type="string"/>  
        <property name="name"/>  
        <property name="sex"/>  
        <subclass name="Pig" discriminator-value="P">  
            <property name="weight"/>  
        </subclass>  
        <subclass name="Bird" discriminator-value="B">  
            <property name="height"/>  
        </subclass>  
    </class>  
</hibernate-mapping>
```

说明：

因为类继承树肯定是对应多个类，要把多个类的信息存放在一张表中，必须有某种机制来区分哪些记录是属于哪个类的。这种机制就是，在表中添加一个字段，用这个字段的值来进行区分。

用hibernate实现这种策略的时候，有如下步骤：

1、父类用普通的`<class>`标签定义

2、在父类中定义一个discriminator，即指定这个区分的字段的名称和类型
如：`<discriminator column=”XXX” type=”string”/>`

3、子类使用`<subclass>`标签定义，在定义subclass的时候，需要注意如下几点：

* Subclass标签的name属性是子类的全路径名
* 在Subclass标签中，用discriminator-value属性来标明本子类的discriminator字段（用来区分不同类的字段）的值
* Subclass既可以被class标签所包含（这种包含关系正是表明了类之间的继承关系），也可以与class标签平行。当subclass标签的定义与class标签平行的时候，需要在subclass标签中，添加extends属性，里面的值是父类的全路径名称。
* 子类的其它属性，像普通类一样，定义在subclass标签的内部。
* 关于鉴别值在存储的时候hibernate会自动存储，在加载的时候会根据鉴别值取得相关的对象


### （二）每个类一张表

关系模型如下：

![][3]

映射文件如下：

```xml
<hibernate-mapping package="com.nnngu">  
    <class name="Animal" table="t_animal">  
        <id name="id">  
            <generator class="native"/>  
        </id>  
        <property name="name"/>  
        <property name="sex"/>  
        <joined-subclass name="Pig" table="t_pig">  
            <key column="pid"/>  
            <property name="weight"/>  
        </joined-subclass>  
        <joined-subclass name="Bird" table="t_bird">  
            <key column="bid"/>  
            <property name="height"/>  
        </joined-subclass>  
    </class>  
</hibernate-mapping>  
```

说明：

这种策略是使用`joined-subclass`标签来定义子类的。父类、子类，每个类都对应一张数据库表。
在父类对应的数据库表中，实际上会存储所有的记录，包括父类和子类的记录；在子类对应的数据库表中，这个表只定义了子类中所特有的属性映射的字段。子类与父类，通过相同的主键值来关联。

实现这种策略的时候，有如下步骤：

1、父类用普通的`<class>`标签定义即可

2、父类不再需要定义`discriminator`字段

3、子类用`<joined-subclass>`标签定义，在定义`joined-subclass`的时候，需要注意如下几点：

* joined-subclass标签的name属性是子类的全路径名
* joined-subclass标签需要包含一个key标签，这个标签指定了子类和父类之间是通过哪个字段来关联的。如：`<key column=”PARENT_KEY_ID”/>`，这里的column，实际上就是父类的主键对应的映射字段名称。
* joined-subclass标签，既可以被class标签所包含（这种包含关系正是表明了类之间的继承关系），也可以与class标签平行。 当joined-subclass标签的定义与class标签平行的时候，需要在joined-subclass标签中，添加extends属性，里面的值是父类的全路径名称。子类的其它属性，像普通类一样，定义在joined-subclass标签的内部。


### （三）每个子类一张表

关系模型如下：

![][4]

映射文件如下：

```xml
<hibernate-mapping package="com.nnngu">  
    <class name="Animal" table="t_animal" abstract="true">  
        <id name="id">  
            <generator class="assigned"/>  
        </id>  
        <property name="name"/>  
        <property name="sex"/>  
        <union-subclass name="Pig" table="t_pig">  
            <property name="weight"/>  
        </union-subclass>  
        <union-subclass name="Bird" table="t_bird">  
            <property name="height"/>  
        </union-subclass>  
    </class>  
</hibernate-mapping>
```

说明：

这种策略是使用union-subclass标签来定义子类的。每个子类对应一张表，而且这个表的信息是完备的，即包含了所有从父类继承下来的属性映射的字段（这就是它跟joined-subclass的不同之处，joined-subclass定义的子类的表，只包含子类特有属性映射的字段）。

实现这种策略的时候，有如下步骤：

1、父类用普通`<class>`标签定义即可

2、子类用`<union-subclass>`标签定义，在定义union-subclass的时候，需要注意如下几点：

* union-subclass标签不再需要包含key标签（与joined-subclass不同）
* union-subclass标签，既可以被class标签所包含（这种包含关系正是表明了类之间的继承关系），也可以与class标签平行。 当union-subclass标签的定义与class标签平行的时候，需要在union-subclass标签中，添加extends属性，里面的值是父类的全路径名称。
* 子类的其它属性，像普通类一样，定义在union-subclass标签的内部。这个时候，虽然在union-subclass里面定义的只有子类的属性，但是因为它继承了父类，所以，不需要定义其它的属性，在映射到数据库表的时候，依然包含了父类的所有属性的映射字段。

注意：在保存对象的时候id不能重复（不能使用数据库的自增方式生成主键）














---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-23-Hibernate%E7%9A%84%E7%BB%A7%E6%89%BF%E6%98%A0%E5%B0%84.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-23-Hibernate%E7%9A%84%E7%BB%A7%E6%89%BF%E6%98%A0%E5%B0%84.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521813220661.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521813432985.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521813827293.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/23/1521814690190.jpg