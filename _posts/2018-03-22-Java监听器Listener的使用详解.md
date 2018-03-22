---
categories: JavaWeb
description: 本文主要介绍各种Java监听器以及他们的使用。
---

监听器用于监听Web应用中某些对象的创建、销毁、增加，修改，删除等动作的发生，然后作出相应的响应处理。当监听范围的对象的状态发生变化的时候，服务器自动调用监听器对象中的方法。常用于统计网站在线人数、系统加载时进行信息初始化、统计网站的访问量等等。

## 分类：

### 按监听的对象划分

可以分为：

* ServletContext对象的监听器
* HttpSession对象的监听器
* ServletRequest对象的监听器

### 按监听的事件划分

可以分为：

* 对象自身的创建和销毁的监听器
* 对象中属性的创建和消除的监听器
* session中的某个对象的状态变化的监听器

## 示例：用监听器统计网站的在线人数

**原理：每当有一个访问连接到服务器时，服务器就会创建一个session来管理会话。那么我们就可以通过统计session的数量来获得当前在线人数。所以这里用到的是HttpSessionListener。**

1、创建监听器类，实现HttpSessionListener接口，并重写监听器类中的方法。代码如下：

```java
public class onLineCount implements HttpSessionListener {

    public int count=0;//记录session的数量
    public void sessionCreated(HttpSessionEvent arg0) {//监听session的创建
        count++;
        arg0.getSession().getServletContext().setAttribute("Count", count);

    }

    @Override
    public void sessionDestroyed(HttpSessionEvent arg0) {//监听session的撤销
        count--;
        arg0.getSession().getServletContext().setAttribute("Count", count);
    }

}
```

2、在web.xml中配置监听器。注意：监听器>过滤器>servlet，配置的时候要注意先后顺序

```xml
<listener>
  <listener-class>com.ygj.control.onLineCount</listener-class>
</listener>
```

如果使用 Servlet3.0 以上版本，监听器的配置可以直接在代码中通过注解来完成，无需在 web.xml 中再配置。如下：

```java
@WebListener   //在此注明以下类是监听器
public class onLineCount implements HttpSessionListener {

    public int count=0;
    public void sessionCreated(HttpSessionEvent arg0) {
        count++;
        arg0.getSession().getServletContext().setAttribute("Count", count);

    }

    @Override
    public void sessionDestroyed(HttpSessionEvent arg0) {
        count--;
        arg0.getSession().getServletContext().setAttribute("Count", count);
    }
```

3、在需要显示在线人数的地方通过`session.getAttribute("Count")`即可获取在线人数值。

## 附：常用监听器

**除了上面监听session建立与销毁的listener外，还有以下几个常用的监听器。**

1、监听session属性的增加、移除以及属性值改变的HttpSessionAttributeListener，如下图：

![][1]

2、监听web上下文的初始化（服务器已准备好接收请求）与销毁的ServletContextListener，如下图：

![][2]

3、监听web上下文属性的增加、删除、属性值变化的ServletContextAttributeListener，如下图：

![][3]

4、监听request的创建与销毁的ServletRequestListener，如下图：

![][4]

5、监听request的属性的增加、删除、属性值变化的ServletRequestAttributeListener，如下图：

![][5]

























---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-Java%E7%9B%91%E5%90%AC%E5%99%A8Listener%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-Java%E7%9B%91%E5%90%AC%E5%99%A8Listener%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521712268679.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521712339086.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521712392621.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521712435429.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521712488700.jpg