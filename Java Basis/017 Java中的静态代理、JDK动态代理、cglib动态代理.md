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
import com.nnngu.dao.Count;    
/** 
 * 委托类(包含业务逻辑) 
 */  
public class CountImpl implements Count {  
  
    @Override  
    public void queryCount() {  
        System.out.println("查看账户...");   
    }  
  
    @Override  
    public void updateCount() {  
        System.out.println("修改账户...");    
    }  
  
}
```

3、定义业务代理类：在代理类中创建一个业务实现类的对象来调用具体的业务方法；

在代理类中实现业务逻辑接口中的方法时：①进行预处理操作、②通过业务实现类的对象调用真正的业务方法、③进行调用后的操作。

```java
public class CountProxy implements Count {  
    private CountImpl countImpl;  // 组合一个业务实现类的对象来进行真正的业务方法的调用
  
    /** 
     * 覆盖默认构造器 
     *  
     * @param countImpl 
     */  
    public CountProxy(CountImpl countImpl) {  
        this.countImpl = countImpl;  
    }  
  
    @Override  
    public void queryCount() {  
        System.out.println("查询账户的预处理——————");  
        // 调用真正的查询账户方法
        countImpl.queryCount();  
        System.out.println("查询账户之后————————");  
    }  
  
    @Override  
    public void updateCount() {  
        System.out.println("修改账户之前的预处理——————");  
        // 调用真正的修改账户操作
        countImpl.updateCount();  
        System.out.println("修改账户之后——————————");  
  
    }  
  
}
```

4、在使用时，首先创建业务实现类对象，然后把业务实现类对象当作构造参数创建一个代理类对象，最后通过代理类对象进行业务方法的调用。

```java
public static void main(String[] args) {  

        CountImpl countImpl = new CountImpl();  
        CountProxy countProxy = new CountProxy(countImpl);  
        countProxy.updateCount();  
        countProxy.queryCount();  
  
    }
```

静态代理的缺点很明显：一个代理类只能对一个业务接口的实现类进行包装，如果有多个业务接口的话就要定义很多实现类和代理类才行。而且，如果代理类对业务方法的预处理、调用后操作都是一样的（比如：调用前输出提示、调用后自动关闭连接），则多个代理类就会有很多重复代码。这时我们可以定义这样一个代理类，它能代理所有实现类的方法调用：根据传进来的业务实现类和方法名进行具体调用。——这就是动态代理。

## 二、JDK动态代理

JDK动态代理所用到的代理类在程序调用到代理类对象时才由JVM真正创建，JVM根据传进来的 业务实现类对象 以及 方法名 ，动态地创建了一个代理类的class文件并被字节码引擎执行，然后通过该代理类对象进行方法调用。我们需要做的，只需指定代理类的预处理、调用后操作即可。

1、首先，定义业务逻辑接口

```java
public interface BookFacade {  
    public void addBook();  
}
```

2、然后，实现业务逻辑接口创建业务实现类

```java
public class BookFacadeImpl implements BookFacade {   
    @Override  
    public void addBook() {  
        System.out.println("增加图书方法。。。");  
    }  
  
}
```

3、最后，实现 调用管理接口`InvocationHandler`  创建动态代理类

```java
public class BookFacadeProxy implements InvocationHandler {  
    private Object target; // 这是业务实现类对象，用来调用具体的业务方法 
	
    /** 
     * 绑定业务对象并返回一个代理类  
     */  
    public Object bind(Object target) {  
        this.target = target;  // 接收业务实现类对象参数

       // 通过反射机制，创建一个代理类对象实例并返回。
       // 创建代理对象时，需要传递该业务类的类加载器、接口、handler实现类
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),  
                target.getClass().getInterfaces(), this); 
	}  
	
    /** 
     * 包装调用方法：进行预处理、调用后处理 
     */  
    public Object invoke(Object proxy, Method method, Object[] args)  
            throws Throwable {  
        Object result=null;  

        System.out.println("预处理操作——————");  
		
        // 调用真正的业务方法  
        result=method.invoke(target, args);  

        System.out.println("调用后处理——————");  
        return result;  
    }  
  
}
```















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/Java%20Basis/017%20Java%E4%B8%AD%E7%9A%84%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81JDK%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81cglib%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86.md](https://github.com/nnngu/LearningNotes/blob/master/Java%20Basis/017%20Java%E4%B8%AD%E7%9A%84%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81JDK%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81cglib%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86.md)