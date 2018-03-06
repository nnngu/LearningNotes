# 04 Spring的@Autowired注解、@Resource注解、@Service注解

## 什么是注解

传统的`Spring`做法是使用`.xml`文件来对`bean`进行注入或者是配置`aop`、事务，这么做有两个缺点：

1、如果所有的内容都配置在`.xml`文件中，那么`.xml`文件将会十分庞大；如果按需求分开`.xml`文件，那么`.xml`文件又会非常多。总之这将导致配置文件的可读性与可维护性变得很低。

2、开发中需要在`.java`文件和`.xml`文件之间不断切换，是一件麻烦的事，同时这种思维上的不连贯也会降低开发的效率。

为了解决这两个问题，`Spring`引入了注解，通过`@XXX`的方式，让注解与Java Bean 紧密结合，既大大减少了配置文件的体积，又增加了Java Bean 的可读性与内聚性。

本篇文章，讲讲最重要的三个Spring注解，也就是`@Autowired`、`@Resource`和`@Service`。

## 不使用注解

先看一个不使用注解的 Spring 示例，在这个示例的基础上，再改成注解版本，这样也能看出使用与不使用注解之间的区别，先定义一个老虎类：

```java
public class Tiger
{
    private String tigerName = "TigerKing";
    
    public String toString()
    {
        return "TigerName:" + tigerName;
    }
}
```

再定义一个猴子类：

```java
public class Monkey
{
    private String monkeyName = "MonkeyKing";
    
    public String toString()
    {
        return "MonkeyName:" + monkeyName;
    }
}
```

再定义一个动物园类：

```java
public class Zoo
{
    private Tiger tiger;
    private Monkey monkey;
    
    public void setTiger(Tiger tiger)
    {
        this.tiger = tiger;
    }
    
    public void setMonkey(Monkey monkey)
    {
        this.monkey = monkey;
    }
    
    public Tiger getTiger()
    {
        return tiger;
    }
    
    public Monkey getMonkey()
    {
        return monkey;
    }
    
    public String toString()
    {
        return tiger + "\n" + monkey;
    }
}
```

在Spring的配置文件中这么写：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
    xmlns="http://www.springframework.org/schema/beans"  
    xmlns:context="http://www.springframework.org/schema/context"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.2.xsd"
    default-autowire="byType">
    
    <bean id="zoo" class="com.nnngu.bean.Zoo" >
        <property name="tiger" ref="tiger" />
        <property name="monkey" ref="monkey" />
    </bean>
    
    <bean id="tiger" class="com.nnngu.domain.Tiger" />
    <bean id="monkey" class="com.nnngu.domain.Monkey" />
    
</beans>
```

都很熟悉，权当复习一遍了。

## @Autowired

`@Autowired`顾名思义，就是自动装配，其作用是为了消除Java代码里面的`getter/setter`与bean属性中的property。当然，`getter`看个人需求，如果私有属性需要对外提供的话，应当保留该`getter`。

因此，引入`@Autowired`注解，先看一下Spring的配置文件怎么写：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
    xmlns="http://www.springframework.org/schema/beans"  
    xmlns:context="http://www.springframework.org/schema/context"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.2.xsd">
    
    <context:component-scan base-package="com.nnngu" />
    
    <bean id="zoo" class="com.nnngu.bean.Zoo" />
    <bean id="tiger" class="com.nnngu.domain.Tiger" />
    <bean id="monkey" class="com.nnngu.domain.Monkey" />
    
</beans>
```

注意上面代码中的`<context:component-scan base-package="com.nnngu" />` ，作用是告诉Spring我要使用注解了，Spring会自动扫描`com.nnngu`路径下的注解。

之前在`zoo`这个bean里面应当注入的两个属性`tiger`、`monkey`，现在不需要注入了。再看一下 `Zoo.java` 也很方便，把`getter/setter` 都可以去掉：

```java
public class Zoo
{
    @Autowired
    private Tiger tiger;
    
    @Autowired
    private Monkey monkey;
    
    public String toString()
    {
        return tiger + "\n" + monkey;
    }
}
```

上面代码中`@Autowired`注解的意思是，当Spring发现`@Autowired`注解时，将自动在代码上下文中找到和其匹配（默认是类型匹配）的Bean，并自动注入到相应的地方去。

（有一个细节性的问题是，假如配置文件的bean里面有两个property，`Zoo.java`里面又去掉了属性的`getter/setter`并使用`@Autowired`注解标注这两个属性那会怎么样？答案是Spring会按照xml优先的原则去`Zoo.java`中寻找这两个属性的`getter/setter`，导致的结果就是初始化bean报错。）

此时如果我把`.xml`文件的`<bean id="tiger" class="com.nnngu.domain.Tiger" />`和`<bean id="monkey" class="com.nnngu.domain.Monkey" />` 去掉，再运行，会抛出如下异常：

```
org.springframework.beans.factory.BeanCreationException: Could not autowire field
```

因为，`@Autowired`注解要去寻找的是一个`bean`，Tiger 和 Monkey 的`bean`定义都给去掉了，自然就不是一个`bean`了，Spring容器找不到也很好理解。那么，如果属性的`bean`找不到，我又不想让Spring容器抛出异常，而只显示null，可以吗？答案是可以的，只要将`@Autowired`注解的required属性设置为 false 即可，如下：

```java
public class Zoo
{
    @Autowired(required = false)
    private Tiger tiger;
    
    @Autowired(required = false)
    private Monkey monkey;
    
    public String toString()
    {
        return tiger + "\n" + monkey;
    }
}
```

此时，就算找不到 tiger、monkey 这两个属性的 `bean`，Spring容器也不再抛出异常，而是认为这两个属性为null。

## @Autowired接口注入

上面的比较简单，我们只是简单注入一个Java类，那么如果有一个接口，有多个实现，Bean里引用的是接口名，又该怎么做呢？比如有一个Car接口：

```java
public interface Car
{
    public String carName();
}
```

两个实现类BMW和Benz：

```java
@Service
public class BMW implements Car
{
    public String carName()
    {
        return "BMW car";
    }
}
```

```java
@Service
public class Benz implements Car
{
    public String carName()
    {
        return "Benz car";
    }
}
```

写一个CarFactory，引用Car：

```java
@Service
public class CarFactory
{
    @Autowired
    private Car car;
    
    public String toString()
    {
        return car.carName();
    }
}
```

不用说，一定是报错的，Car接口有两个实现类，Spring并不知道应当引用哪个实现类。这种情况通常有两个解决办法：

1、删除其中一个实现类，Spring会自动去base-package下寻找Car接口的实现类，发现Car接口只有一个实现类，便会直接引用这个实现类。

2、实现类就是有多个该怎么办？此时可以使用@Qualifier注解：

```java
@Service
public class CarFactory
{
    @Autowired
    @Qualifier("BMW")
    private Car car;
    
    public String toString()
    {
        return car.carName();
    }
}
```

注意`@Qualifier`注解括号里面的应当是Car接口实现类的类名，我之前试的时候一直以为是bean的名字，所以写了"bMW"，结果一直报错。

## @Resource

把`@Resource`注解放在`@Autowired`下面说，是因为它们的作用非常相似。先看一下`@Resource`，直接在`Zoo.java`中写：

```java
@Service
public class Zoo
{
    @Resource(name = "tiger")
    private Tiger tiger;
    
    @Resource(type = Monkey.class)
    private Monkey monkey;
    
    public String toString()
    {
        return tiger + "\n" + monkey;
    }
}
```

说一下`@Resource`的装配顺序：

1、`@Resource`后面如果没有任何内容，默认是通过name属性去匹配bean，找不到再按type去匹配

2、指定了name或者type则根据指定的类型去匹配bean

3、指定了name和type则根据指定的name和type去匹配bean，任何一个不匹配都将报错

然后，区分一下`@Autowired`和`@Resource`两个注解的区别：

1、`@Autowired`默认按照byType方式进行bean匹配，`@Resource`默认按照byName方式进行bean匹配

2、`@Autowired`是Spring的注解，`@Resource`是J2EE的注解，这个可以看一下导入注解的时候这两个注解的包名就一清二楚了

Spring属于第三方的，J2EE是Java自己的东西，因此，建议使用`@Resource`注解，以减少代码和Spring之间的耦合。

## @Service

上面动物园这个例子，还可以继续简化，因为Spring的配置文件里面还有三个bean，下一步的简化是把这三个bean也给去掉，使得Spring配置文件里面只有一个自动扫描的标签。这样做增强了Java代码的内聚性并进一步减少配置文件。

先看一下配置文件（三个bean都删除了）：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
    xmlns="http://www.springframework.org/schema/beans"  
    xmlns:context="http://www.springframework.org/schema/context"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.2.xsd">
    
    <context:component-scan base-package="com.nnngu" />
    
</beans>
```

下面以`Zoo.java`为例，其余的`Monkey.java`和`Tiger.java`都一样：

```java
@Service
public class Zoo
{
    @Autowired
    private Tiger tiger;
    
    @Autowired
    private Monkey monkey;
    
    public String toString()
    {
        return tiger + "\n" + monkey;
    }
}
```

这样，`Zoo.java`在Spring容器中存在的形式就是`zoo`，即可以通过			`ApplicationContext`的`getBean("zoo")`方法来得到`Zoo.java`。`@Service`注解，其实做了两件事情：

1、声明`Zoo.java`是一个bean，这点很重要，因为`Zoo.java`是一个bean，其他的类才可以使用`@Autowired`将Zoo作为一个成员变量自动注入

2、`Zoo.java`在bean中的id是`zoo`，即类名且首字母小写

如果我不想用这种形式，而想让`Zoo.java`在Spring容器中的名字就叫做`Zoo`，可以吗？答案是可以的：

```java
@Service("Zoo")
@Scope("prototype")
public class Zoo
{
    @Autowired
    private Monkey monkey;
    @Autowired
    private Tiger tiger;
    
    public String toString()
    {
        return tiger + "\n" + monkey;
    }
}
```

这样，就可以通过`ApplicationContext`的`getBean("Zoo")`方法来得到`Zoo.java`了。

这里我还多加了一个`@Scope`注解，应该很好理解。因为Spring默认产生的bean是单例的，假如我不想使用单例怎么办，xml文件里面可以在bean里面配置scope属性。注解也一样，配置`@Scope`即可，默认是"singleton"即单例，"prototype"表示原型即每次都会new一个新的对象出来。

## 补充细节

最后再补充一个细节。假如animal包下有Tiger，domain包下也有Tiger，它们二者都加了`@Service`注解，那么在`Zoo.java`中即使明确表示我要引用的是domain包下的Tiger，程序运行的时候依然会报错。

因为两个Tiger都使用`@Service`注解标注，意味着两个bean的名字都是"tiger"，那么我在`Zoo.java`中自动装配的是哪个tiger呢？不明确，因此，Spring容器会抛出`ConflictingBeanDefinitionException`异常。





















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Spring/04%20Spring%E7%9A%84%40Autowired%E6%B3%A8%E8%A7%A3%E3%80%81%40Resource%E6%B3%A8%E8%A7%A3%E3%80%81%40Service%E6%B3%A8%E8%A7%A3.md](https://github.com/nnngu/LearningNotes/blob/master/Spring/04%20Spring%E7%9A%84%40Autowired%E6%B3%A8%E8%A7%A3%E3%80%81%40Resource%E6%B3%A8%E8%A7%A3%E3%80%81%40Service%E6%B3%A8%E8%A7%A3.md)