# 014 Java的反射机制

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

这篇文章要总结java的反射机制，将从以下几点进行总结：

一、什么是反射机制

二、哪里用到反射机制

三、反射机制的优点与缺点

四、利用反射机制能获得什么信息

五、反射创建 Class 对象的三种方式



## 一、什么是反射机制

简单的来说，反射机制指的是程序在运行时能够获取自身的信息。在 java 中，只要给定类的名字，那么就可以通过反射机制来获得类的所有信息。

## 二、哪里用到反射机制

有些时候，我们用过一些知识，但是并不知道它的专业术语是什么，比如在学jdbc时用过这一行代码，  `Class.forName("com.mysql.jdbc.Driver.class").newInstance();`  

但是那时候只知道这行代码是生成驱动对象实例，并不知道它的具体含义。学习了反射机制之后才知道，原来这就是反射，现在很多开源框架都用到反射机制，spring、hibernate、struts都是用反射机制实现的。

## 三、反射机制的优点与缺点

为什么要用反射机制？直接创建对象不就可以了吗？这就涉及到了动态与静态的概念。

静态编译：在编译时确定类型，绑定对象。 
 
动态编译：运行时确定类型，绑定对象。动态编译最大限度发挥了java的灵活性，体现了多态的应用，由此降低类之间的藕合性。 

一句话，反射机制的优点就是可以实现动态创建对象和编译，体现出很大的灵活性，特别是在J2EE的开发中，它的灵活性就表现的十分明显。

比如，一个大型的软件，不可能一次就把它设计的很完美，当这个程序编译后，发布了，当发现需要更新某些功能时，我们不可能要用户把以前的卸载，再重新安装新的版本，假如这样的话，这个软件肯定是没有多少人用的。采用静态的话，需要把整个程序重新编译一次才可以实现功能的更新，而采用反射机制的话，它就可以不用卸载，只需要在运行时才动态的创建和编译，就可以实现该功能。 

它的缺点是对性能有影响。使用反射基本上是一种解释操作，我们可以告诉JVM，我们希望做什么并且它满足我们的要求。这类操作总是慢于只直接执行相同的操作。 

## 四、利用反射机制能获得什么信息

一句话，类中有什么信息，它就可以获得什么信息，不过前提是得知道类的名字，要不然就没有后文了。

首先得根据传入的类的全名来创建Class对象。`Class c=Class.forName("className");`

注明：className必须为全名，也就是要包含包名，比如，`cn.netjava.pojo.UserInfo;`

`Object obj=c.newInstance(); // 创建对象的实例 `

OK，有了对象就什么都好办了，想要什么信息就有什么信息了。   

获得构造器的方法 

```java
Constructor getConstructor(Class[] params) // 根据指定参数获得public构造器

Constructor[] getConstructors() // 获得public的所有构造器

Constructor getDeclaredConstructor(Class[] params) // 根据指定参数获得public和非public的构造器

Constructor[] getDeclaredConstructors() // 获得public的所有构造器 
```

获得方法的方法 
```java
    Method getMethod(String name, Class[] params) // 根据方法名，参数类型获得方法

    Method[] getMethods() // 获得所有的public方法

    Method getDeclaredMethod(String name, Class[] params) // 根据方法名和参数类型，获得public和非public的方法

    Method[] getDeclaredMethods() // 获得所以的public和非public方法 
```

获得类中属性的方法 
```java
    Field getField(String name) // 根据变量名得到相应的public变量

    Field[] getFields() // 获得类中所以public的方法

    Field getDeclaredField(String name) // 根据方法名获得public和非public变量

    Field[] getDeclaredFields() // 获得类中所有的public和非public方法
```
常用的就这些，知道这些，其他的都好办……

## 五、反射创建 Class 对象的三种方式

```java
// 创建对象的方式一：( 对象.getClass() )
Class class1 = p1.getClass();
           
// 创建对象的方式二：( 类.class 需要一个明确的类 )
Class class2 = Person.class;

// 创建对象的方式三：( Class.forName() 传入时需要以字符串的方式传入 )
Class class3 = Class.forName("com.Person"); // 异常ClassNotFoundException
```