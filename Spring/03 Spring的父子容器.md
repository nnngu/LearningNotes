# 03 Spring的父子容器

## 1、概念理解和知识铺垫

在Spring整体框架的核心概念中，容器是核心思想，就是用来管理Bean的整个生命周期的，而在一个项目中，容器不一定只有一个，Spring中可以包括多个容器，而且容器有上下层关系，目前最常见的一种场景就是在一个项目中引入Spring和SpringMVC这两个框架，那么它其实就是两个容器，Spring是父容器，SpringMVC是其子容器，并且在父容器中注册的Bean对于子容器是可见的，而在子容器中注册的Bean对于父容器是不可见的，也就是子容器可以看见父容器中的注册的Bean，反之就不行。

我们可以使用统一的如下注解配置来对Bean进行批量注册，而不需要再给每个Bean单独使用xml的方式进行配置。

```xml
<context:component-scan base-package="com.nnngu" />
```

从Spring提供的参考手册中我们得知该配置的功能是扫描配置的base-package包下的所有使用了\@Component注解的类，并且将它们自动注册到容器中，同时也扫描\@Controller，\@Service，\@Respository这三个注解，因为他们是继承自\@Component。

在项目中我们经常见到还有如下这个配置，其实有了上面的配置，这个是可以省略掉的，因为上面的配置会默认打开以下配置。以下配置会默认声明了\@Required、\@Autowired、 \@PostConstruct、\@PersistenceContext、\@Resource、\@PreDestroy等注解。

```xml
<context:annotation-config/>
```

另外，还有一个和SpringMVC相关如下配置，经过验证，这个是SpringMVC必须要配置的，因为它声明了\@RequestMapping、\@RequestBody、\@ResponseBody等。并且，该配置默认加载很多的参数绑定方法，比如json转换解析器等。

```xml
<mvc:annotation-driven />
```

## 2、使用场景分析

我们共有Spring和SpringMVC两个容器，它们的配置文件分别为applicationContext.xml和applicationContext-MVC.xml。

1. 在applicationContext.xml中配置了`<context:component-scan base-package=“com.nnngu" />`，负责所有需要注册的Bean的扫描和注册工作。

2. 在applicationContext-MVC.xml中配置`<mvc:annotation-driven />`，负责SpringMVC相关注解的使用。

3. 启动项目我们发现SpringMVC无法进行跳转，将log的日志打印级别设置为DEBUG进行调试，发现SpringMVC容器中的请求好像没有映射到具体controller中。

4. 在applicationContext-MVC.xml中配置`<context:component-scan base-package=“com.nnngu" />`，重启后，验证成功，springMVC跳转有效。

下面我们来查看具体原因，翻看源码，从SpringMVC的DispatcherServlet开始往下找，我们发现SpringMVC初始化时，会寻找SpringMVC容器中的所有使用了\@Controller注解的Bean，来确定其是否是一个handler。1,2两步的配置使得当前springMVC容器中并没有注册带有\@Controller注解的Bean，而是把所有带有\@Controller注解的Bean都注册在Spring这个父容器中了，所以springMVC找不到处理器，不能进行跳转。

而在第4步配置中，SpringMVC容器中也注册了所有带有\@Controller注解的Bean，故SpringMVC能找到处理器进行处理，从而正常跳转。

## 3、官方推荐配置

在实际工程中会包括很多配置，我们按照官方推荐根据不同的业务模块来划分不同容器中注册不同类型的Bean：Spring父容器负责所有其他非\@Controller注解的Bean的注册，而SpringMVC只负责\@Controller注解的Bean的注册，使得他们各负其责、明确边界。配置方式如下：

1. 在applicationContext.xml中配置:

```xml
<!-- Spring容器中注册非@Controller注解的Bean -->
<context:component-scan base-package="com.nnngu">
   <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

2. applicationContext-MVC.xml中配置

```xml
<!-- SpringMVC容器中只注册带有@Controller注解的Bean -->
<context:component-scan base-package="com.nnngu" use-default-filters="false">
   <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller" />
</context:component-scan>
```

## 4、总结

我们在清楚了Spring和SpringMVC的父子容器关系、以及扫描注册的原理以后，根据官方建议，就可以很好把不同类型的Bean分配到不同的容器中进行管理。出现Bean找不到或者SpringMVC不能跳转以及事务的配置失效的问题时，我们就可以很快的定位以及解决问题了。













---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Spring/03%20Spring%E7%9A%84%E7%88%B6%E5%AD%90%E5%AE%B9%E5%99%A8.md](https://github.com/nnngu/LearningNotes/blob/master/Spring/03%20Spring%E7%9A%84%E7%88%B6%E5%AD%90%E5%AE%B9%E5%99%A8.md)