# 017 Java中的静态代理、JDK动态代理、cglib动态代理

## 一、静态代理

代理模式是常用设计模式的一种，我们在软件设计时常用的代理一般是指静态代理，也就是在代码中显式指定的代理。

静态代理由业务实现类、业务代理类两部分组成。业务实现类负责实现主要的业务方法，业务代理类负责对调用的业务方法作拦截、过滤、预处理。在需要调用业务时，不是直接通过业务实现类来调用的，而是通过业务代理类的同名方法来调用被处理过的业务方法。

### 静态代理的实现：

1、首先定义一个接口，说明业务逻辑。 

```java
package com.nnngu.dao;      
    /** 
     * 定义一个账户接口 
     */  
    public interface Count {  
        // 查询账户
        public void queryCount();  
      
        // 修改账户  
        public void updateCount();  
      
    }
```

2、然后定义业务实现类，实现业务逻辑接口。

```java

```













---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Java%20Basis/017%20Java%E4%B8%AD%E7%9A%84%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81JDK%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81cglib%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86.md](https://github.com/nnngu/LearningNotes/blob/master/Java%20Basis/017%20Java%E4%B8%AD%E7%9A%84%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81JDK%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81cglib%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86.md)