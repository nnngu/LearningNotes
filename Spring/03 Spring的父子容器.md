# 03 Spring的父子容器

## 1、概念理解和知识铺垫

在Spring整体框架的核心概念中，容器是核心思想，就是用来管理Bean的整个生命周期的，而在一个项目中，容器不一定只有一个，Spring中可以包括多个容器，而且容器有上下层关系，目前最常见的一种场景就是在一个项目中引入Spring和SpringMVC这两个框架，那么它其实就是两个容器，Spring是父容器，SpringMVC是其子容器，并且在父容器中注册的Bean对于子容器是可见的，而在子容器中注册的Bean对于父容器是不可见的，也就是子容器可以看见父容器中的注册的Bean，反之就不行。

我们可以使用统一的如下注解配置来对Bean进行批量注册，而不需要再给每个Bean单独使用xml的方式进行配置。

```xml
<context:component-scan base-package="com.nnngu" />
```

从Spring提供的参考手册中我们得知该配置的功能是扫描配置的base-package包下的所有使用了@Component注解的类，并且将它们自动注册到容器中，同时也扫描@Controller，@Service，@Respository这三个注解，因为他们是继承自@Component。

在项目中我们经常见到还有如下这个配置，其实有了上面的配置，这个是可以省略掉的，因为上面的配置会默认打开以下配置。以下配置会默认声明了@Required、@Autowired、 @PostConstruct、@PersistenceContext、@Resource、@PreDestroy等注解。

```xml
<context:annotation-config/>
```

另外，还有一个和SpringMVC相关如下配置，经过验证，这个是SpringMVC必须要配置的，因为它声明了@RequestMapping、@RequestBody、@ResponseBody等。并且，该配置默认加载很多的参数绑定方法，比如json转换解析器等。

```xml
<mvc:annotation-driven />
```

















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Spring/03%20Spring%E7%9A%84%E7%88%B6%E5%AD%90%E5%AE%B9%E5%99%A8.md](https://github.com/nnngu/LearningNotes/blob/master/Spring/03%20Spring%E7%9A%84%E7%88%B6%E5%AD%90%E5%AE%B9%E5%99%A8.md)