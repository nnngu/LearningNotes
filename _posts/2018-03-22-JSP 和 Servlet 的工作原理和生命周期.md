---
categories: JavaWeb
description: 本文总结 JSP 和 Servlet 的工作原理和生命周期。
---

JSP的英文名叫Java Server Pages，翻译为中文是Java服务器页面的意思，其底层就是一个简化的Servlet设计，是由sum公司主导参与建立的一种动态网页技术标准。Servlet 就是 Java 编程语言中的一个类，它被用来扩展服务器的性能。

## JSP的执行过程和生命周期

JSP的执行过程和生命周期，如下图：

![][1]

## Servlet的生命周期

Servlet的生命周期主要分为以下三个阶段：一是容器初始化。即`init()`，二是调用`service()`方法，判断客户端请求的方式。最后是销毁，调用`destroy()`方法。

详细的 Servlet 生命周期示意图如下：

![][2]

## JSP与Servlet的优缺点比较

* JSP优点：提高代码的可复用性、将HTML代码进行分离、程序利于开发维护。
* JSP缺点：不容易跟踪与排错。不能处理流程和业务逻辑。
* Servlet优点是响应客户端的请求，根据请求动态响应，最大的优点是作为一个服务，控制程序的流向，过滤等。MVC中的C就是servlet。
* Servlet缺点：Servlet在表示逻辑上对于视图的表示相对于JSP麻烦太多，在负责显示工作完成并生成页面上，JSP更优。

## 编写第一个JSP文件

编写第一个JSP文件，为解决跳转路径问题，可在头部加上

```jsp
<%    
String path = request.getContextPath();    
String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
```

如下图：

![][3]

## 编写第一个Servlet程序

编写第一个Servlet程序，这里使用Servlet3.0，不需在web.xml中配置，可自己设置名称，但必须要与页面中form表单中的action对应。如下图：

![][4]


















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-JSP%20%E5%92%8C%20Servlet%20%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%92%8C%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-JSP%20%E5%92%8C%20Servlet%20%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%92%8C%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521695280116.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521696361730.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521696030375.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521696228346.jpg