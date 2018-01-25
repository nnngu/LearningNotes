# 016 Java中的动态代理
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---

## 代理

代理模式是一种常用的设计模式，其目的就是为其他对象提供一个代理以控制对某个真实对象的访问。代理类负责为委托类预处理消息，过滤消息并转发消息，以及进行消息被委托类执行后的后续处理。

代理可以实现过滤请求、插入横切逻辑等功能，应用场景丰富多彩。

代理的方式分为静态代理和动态代理两种。

### 静态代理

程序运行前代理类的字节码文件依然存在，需要程序员编写源文件。

* 缺点：要针对于每一个类撰写代理类；对于单个被代理的类，如果需要被代理的方法很多，又加大了工作量。

* 优点：直观，可读性较强。

### 动态代理

程序运行时动态生成代理类的字节码文件，不需要程序员编写代理类java文件。

* 缺点：由于是运行时动态生成的，因此可读性不是很强；而且受限于被代理类自身的属性（jdk需要提供接口，cglib需要是非私有类）。

* 优点：代码更加简洁，解放了无谓的编码工作。

## 实现方式

让你来实现一个代理类，需要哪些上下文，有哪些解决方案？

jdk和cglib两种解决方案。

要生产一个类A的代理类，唯一需要了解的就是生成一个什么类，因此就有了基于该类的接口构造一个“A”。

至于如何生成一个class文件，在既定规则下你当然可以先生产java文件，再编译成class文件。而最好的做法是直接操作字节码文件，jdk和cglib生成字节码文件分别用了sun的ProxyGenerator和开源项目ASM字节码框架。

![][1]

通过代理层这一中间层，有效的控制对于真实委托类对象的直接访问，同时可以实现自定义的控制策略（Spring的AOP机制），设计上获得更大的灵活性。

## JDK的动态代理实现

jdk的动态代理，依赖的是反射包下的InvocationHandler接口，我们的代理类实现InvocationHandler，重写invoke()方法，每当我们的代理类调用方法时，都会默认先经过invoke()方法。

*UserService接口*
```java
public interface UserService {
    public String getName(int id);
    public Integer getAge(int id);
}
```

*接口的实现类UserServiceImpl*
```java
public class UserServiceImpl implements UserService {

    public String getName(int id) {
        System.out.println("------getName------");
        return "Tom";
    }

    public Integer getAge(int id) {
        System.out.println("------getAge------");
        return 10;
    }
}
```

*UserInvocationHandler.java*
```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class UserInvocationHandler implements InvocationHandler {

    private Object target;

    UserInvocationHandler() {
        super();
    }

    UserInvocationHandler(Object target) {
        super();
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if("getName".equals(method.getName())){
            System.out.println("++++++before " + method.getName() + "++++++");
            Object result = method.invoke(target, args);
            System.out.println("++++++after " + method.getName() + "++++++");
            return result;
        }else{
            Object result = method.invoke(target, args);
            return result;
        }
    }
}
```

*测试类TestUserInvocationHandler.java*
```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Proxy;

public class TestUserInvocationHandler {
    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        InvocationHandler invocationHandler = new UserInvocationHandler(userService);

        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(
                userService.getClass().getClassLoader(),
                userService.getClass().getInterfaces(),
                invocationHandler);

        System.out.println(userServiceProxy.getName(1));
        System.out.println(userServiceProxy.getAge(1));
    }
}
```

运行结果：

![][2]

## cglib的动态代理实现

cglib需要的jar包：[cglib.jar](https://github.com/nnngu/SharedResource/raw/master/jar/cglib-2.2.2.jar) 和 [asm.jar](https://github.com/nnngu/SharedResource/raw/master/jar/asm-3.3.1.jar)

cglib依赖的是cglib包下的MethodInterceptor接口，每调用代理类的方法，都会调用intercept方法

*CglibMethodInterceptor.java*
```java
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class CglibMethodInterceptor implements MethodInterceptor {
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("------before " + methodProxy.getSuperName() + "------");
        Object o1 = methodProxy.invokeSuper(o, args);
        System.out.println("------after " + methodProxy.getSuperName() + "------");
        return o1;
    }
}

```

*TestCglibMethodInterceptor.java*
```java
import net.sf.cglib.proxy.Enhancer;

public class TestCglibMethodInterceptor {
    public static void main(String[] args) {
        CglibMethodInterceptor cglibProxy = new CglibMethodInterceptor();

        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(UserServiceImpl.class);
        enhancer.setCallback(cglibProxy);

        UserService o = (UserService) enhancer.create();
        o.getName(1);
        o.getAge(1);
    }
}

```

运行结果：
![][3]


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/26/1516913969285.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/26/1516916430119.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/26/1516918841007.jpg