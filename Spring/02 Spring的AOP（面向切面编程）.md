# 02 Spring的AOP（面向切面编程）

## 1、关于AOP

AOP（Aspect Oriented Programming），即面向切面编程，可以说是OOP（Object Oriented Programming，面向对象编程）的补充和完善。OOP引入封装、继承、多态等概念来建立一种对象层次结构，用于模拟公共行为的一个集合。OOP允许开发者定义纵向的关系，但并不适合定义横向的关系，例如日志功能。日志代码往往横向地散布在所有对象层次中，而与它对应的对象的核心功能毫无关系，对于其他类型的代码，如安全性、异常处理等等也是如此，这种散布在各处的无关的代码被称为横切（cross cutting），在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。

AOP技术恰恰相反，它利用一种称为"横切"的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为"Aspect"，即切面。所谓"切面"，简单说就是将那些与业务无关，却为业务模块所共同调用的逻辑封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。

使用"横切"技术，AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处基本相似，比如 权限认证、日志、事务等等。AOP的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。

## 2、AOP的核心概念

1、横切关注点

对哪些方法进行拦截，拦截后怎么处理，这些关注点称之为横切关注点

2、切面（aspect）

类是对物体特征的抽象，切面就是对横切关注点的抽象

3、连接点（joinpoint）

被拦截到的点，因为Spring只支持方法类型的连接点，所以在Spring中连接点指的就是被拦截到的方法，实际上连接点还可以是字段或者构造器

4、切入点（pointcut）

对连接点进行拦截的定义

5、通知（advice）

所谓通知指的就是拦截到连接点之后要执行的代码，通知分为五类：前置、后置、异常、最终、环绕。

6、目标对象

代理的目标对象

7、织入（weave）

将切面应用到目标对象并导致代理对象创建的过程

8、引入（introduction）

在不修改代码的前提下，引入可以在运行期为类动态地添加一些方法或字段

## 3、Spring对AOP的支持

Spring中AOP代理由Spring的IoC容器负责生成、管理，其依赖关系也由IoC容器负责管理。因此，AOP代理可以直接使用容器中的其它bean实例作为目标，这种关系可由IoC容器的依赖注入提供。Spring创建代理的规则为：

1、默认使用Java动态代理来创建AOP代理，这样就可以为任何接口实例创建代理了

2、当需要代理的类不是代理接口的时候，Spring会切换为使用CGLIB代理，也可强制使用CGLIB

AOP编程其实是很简单的事情，纵观AOP编程，程序员只需要参与三个部分：

1、定义普通业务组件

2、定义切入点，一个切入点可能横切多个业务组件

3、定义增强处理，增强处理就是在AOP框架为普通业务组件织入的处理动作

所以进行AOP编程的关键就是定义切入点和定义增强处理，一旦定义了合适的切入点和增强处理，AOP框架将自动生成AOP代理，即：代理对象的方法=增强处理+被代理对象的方法。

下面给出一个Spring AOP的.xml文件模板，名字叫做aop.xml，之后的内容都在aop.xml上进行扩展：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">
            
</beans>
```

## 4、基于Spring的AOP简单实现

在讲解之前，说明一点：使用Spring AOP，要成功运行代码，只用Spring提供给开发者的jar包是不够的，请额外上网下载两个jar包：

1、aopalliance.jar

2、aspectjweaver.jar

开始讲解用Spring AOP的XML实现方式，先定义一个接口：

```java
public interface HelloWorld
{
    void printHelloWorld();
    void doPrint();
}
```

定义两个接口实现类：

```java
public class HelloWorldImpl1 implements HelloWorld
{
    public void printHelloWorld()
    {
        System.out.println("Enter HelloWorldImpl1.printHelloWorld()");
    }
    
    public void doPrint()
    {
        System.out.println("Enter HelloWorldImpl1.doPrint()");
        return ;
    }
}
```

```java
public class HelloWorldImpl2 implements HelloWorld
{
    public void printHelloWorld()
    {
        System.out.println("Enter HelloWorldImpl2.printHelloWorld()");
    }
    
    public void doPrint()
    {
        System.out.println("Enter HelloWorldImpl2.doPrint()");
        return ;
    }
}
```

横切关注点，这里是打印时间：

```java
public class TimeHandler
{
    public void printTime()
    {
        System.out.println("CurrentTime = " + System.currentTimeMillis());
    }
}
```

有这三个类就可以实现一个简单的Spring AOP了，看一下aop.xml的配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">
        
        <bean id="helloWorldImpl1" class="com.nnngu.aop.HelloWorldImpl1" />
        <bean id="helloWorldImpl2" class="com.nnngu.aop.HelloWorldImpl2" />
        <bean id="timeHandler" class="com.nnngu.aop.TimeHandler" />
        
        <aop:config>
            <aop:aspect id="time" ref="timeHandler">
                <aop:pointcut id="addTime" expression="execution(* com.nnngu.aop.HelloWorld.*(..))" />
                <aop:before method="printTime" pointcut-ref="addTime" />
                <aop:after method="printTime" pointcut-ref="addTime" />
            </aop:aspect>
        </aop:config>
</beans>
```

写一个main函数调用一下：

```java
public static void main(String[] args)
{
    ApplicationContext ctx = 
            new ClassPathXmlApplicationContext("aop.xml");
        
    HelloWorld hw1 = (HelloWorld)ctx.getBean("helloWorldImpl1");
    HelloWorld hw2 = (HelloWorld)ctx.getBean("helloWorldImpl2");
    hw1.printHelloWorld();
    System.out.println(); // 换行
    hw1.doPrint();
    
    System.out.println(); // 换行
    hw2.printHelloWorld();
    System.out.println(); // 换行
    hw2.doPrint();
}
```

运行结果为：

```
CurrentTime = 1446129611993
Enter HelloWorldImpl1.printHelloWorld()
CurrentTime = 1446129611993

CurrentTime = 1446129611994
Enter HelloWorldImpl1.doPrint()
CurrentTime = 1446129611994

CurrentTime = 1446129611994
Enter HelloWorldImpl2.printHelloWorld()
CurrentTime = 1446129611994

CurrentTime = 1446129611994
Enter HelloWorldImpl2.doPrint()
CurrentTime = 1446129611994
```

可以看到给HelloWorld接口的两个实现类的所有方法都加上了代理，代理内容就是打印时间。

## 5、基于Spring的AOP使用其他细节

### 5-1、增加一个横切关注点，打印日志，Java类为：

```java
public class LogHandler
{
    public void LogBefore()
    {
        System.out.println("Log before method");
    }
    
    public void LogAfter()
    {
        System.out.println("Log after method");
    }
}
```

aop.xml配置为：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">
        
        <bean id="helloWorldImpl1" class="com.nnngu.aop.HelloWorldImpl1" />
        <bean id="helloWorldImpl2" class="com.nnngu.aop.HelloWorldImpl2" />
        <bean id="timeHandler" class="com.nnngu.aop.TimeHandler" />
        <bean id="logHandler" class="com.nnngu.aop.LogHandler" />
        
        <aop:config>
            <aop:aspect id="time" ref="timeHandler" order="1">
                <aop:pointcut id="addTime" expression="execution(* com.nnngu.aop.HelloWorld.*(..))" />
                <aop:before method="printTime" pointcut-ref="addTime" />
                <aop:after method="printTime" pointcut-ref="addTime" />
            </aop:aspect>
            <aop:aspect id="log" ref="logHandler" order="2">
                <aop:pointcut id="printLog" expression="execution(* com.nnngu.aop.HelloWorld.*(..))" />
                <aop:before method="LogBefore" pointcut-ref="printLog" />
                <aop:after method="LogAfter" pointcut-ref="printLog" />
            </aop:aspect>
        </aop:config>
</beans>
```

测试类不变，打印结果为：

```
CurrentTime = 1446130273734
Log before method
Enter HelloWorldImpl1.printHelloWorld()
Log after method
CurrentTime = 1446130273735

CurrentTime = 1446130273736
Log before method
Enter HelloWorldImpl1.doPrint()
Log after method
CurrentTime = 1446130273736

CurrentTime = 1446130273736
Log before method
Enter HelloWorldImpl2.printHelloWorld()
Log after method
CurrentTime = 1446130273736

CurrentTime = 1446130273737
Log before method
Enter HelloWorldImpl2.doPrint()
Log after method
CurrentTime = 1446130273737
```

要想让logHandler在timeHandler前使用有两个办法：

（1）aspect里面有一个order属性，order属性的数字就是横切关注点的顺序

（2）在aop.xml里，把logHandler定义在timeHandler前面，Spring默认以aspect的定义顺序作为织入顺序

### 5-2、我只想织入接口中的某些方法

修改一下pointcut的expression就好了：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">
        
        <bean id="helloWorldImpl1" class="com.nnngu.aop.HelloWorldImpl1" />
        <bean id="helloWorldImpl2" class="com.nnngu.aop.HelloWorldImpl2" />
        <bean id="timeHandler" class="com.nnngu.aop.TimeHandler" />
        <bean id="logHandler" class="com.nnngu.aop.LogHandler" />
        
        <aop:config>
            <aop:aspect id="time" ref="timeHandler" order="1">
                <aop:pointcut id="addTime" expression="execution(* com.nnngu.aop.HelloWorld.print*(..))" />
                <aop:before method="printTime" pointcut-ref="addTime" />
                <aop:after method="printTime" pointcut-ref="addTime" />
            </aop:aspect>
            <aop:aspect id="log" ref="logHandler" order="2">
                <aop:pointcut id="printLog" expression="execution(* com.nnngu.aop.HelloWorld.do*(..))" />
                <aop:before method="LogBefore" pointcut-ref="printLog" />
                <aop:after method="LogAfter" pointcut-ref="printLog" />
            </aop:aspect>
        </aop:config>
</beans>
```

以上的修改，表示timeHandler只会织入HelloWorld接口print开头的方法，logHandler只会织入HelloWorld接口do开头的方法

### 5-3、强制使用CGLIB生成代理

前面说过Spring使用JDK动态代理或是CGLIB生成代理是有规则的，高版本的Spring会自动选择是使用JDK动态代理还是CGLIB生成代理内容，当然我们也可以强制使用CGLIB生成代理，那就是<aop:config>里面有一个"proxy-target-class"属性，这个属性值如果被设置为true，那么基于类的代理将起作用，如果proxy-target-class被设置为false或者这个属性被省略，那么基于接口的代理将起作用。
















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Spring/02%20Spring%E7%9A%84AOP%EF%BC%88%E9%9D%A2%E5%90%91%E5%88%87%E9%9D%A2%E7%BC%96%E7%A8%8B%EF%BC%89.md](https://github.com/nnngu/LearningNotes/blob/master/Spring/02%20Spring%E7%9A%84AOP%EF%BC%88%E9%9D%A2%E5%90%91%E5%88%87%E9%9D%A2%E7%BC%96%E7%A8%8B%EF%BC%89.md)