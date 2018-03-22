---
categories: JavaWeb
description: 本文主要介绍Java过滤器以及它的使用。
---

## 过滤器

过滤器是处于客户端与服务器资源文件之间的一道过滤网，在访问资源文件之前，通过一系列的过滤器对请求进行修改、判断等，把不符合规则的请求在中途拦截或修改。也可以对响应进行过滤，拦截或修改响应。

如下图，浏览器发出的请求先递交给第一个filter进行过滤，符合规则则放行，递交给filter链中的下一个过滤器进行过滤。过滤器在链中的顺序与它在web.xml中配置的顺序有关，配置在前的则位于链的前端。当请求通过了链中所有过滤器后就可以访问资源文件了，如果不能通过，则可能在中间某个过滤器中被处理掉。

![][1]

过滤器一般用于登录权限验证、资源访问权限控制、敏感词汇过滤、字符编码转换等等操作，便于代码的重用，不必每个servlet中还要进行相应的操作。

## 过滤器的简单应用：

1、新建一个class，实现接口Filter（注意：是javax.servlet中的Filter）。

2、重写过滤器的doFilter(request，response，chain)方法。另外两个init()、destroy()方法一般不需要重写。在doFilter方法中进行过滤操作。

3、在web.xml中配置过滤器。这里要谨记一条原则：在web.xml中，监听器\>过滤器\>servlet。也就是说web.xml中监听器配置在过滤器之前，过滤器配置在servlet之前，否则会出错。

```xml
<filter>  
    <filter-name>loginFilter</filter-name>//过滤器名称  
    <filter-class>com.nnngu.filter.loginFilter</filter-class>//过滤器类的包路径
	<init—param> //可选 
		<param—name>参数名</param-name>//过滤器初始化参数
		<param-value>参数值</param-value>  
	</init—pamm>  
</filter> 
 
<filter-mapping>//过滤器映射  
    <filter-name>loginFilter</filter-name>  
    <url—pattern>指定过滤器作用的范围</url-pattern>
</filter-mapping>
```

`<url-pattren>`处定义过滤器作用的范围。一般有以下规则：

1、作用与所有web资源：`<url—pattern>/*</url-pattern>`。则客户端请求访问任意资源文件时都要经过过滤器的过滤，通过则可以访问，否则不能访问。

2、作用于某一文件夹下所有文件：`<url—pattern>/dir/*</url-pattern>`

3、作用于某一种类型的文件：`<url—pattern>*.扩展名</url-pattern>`。比如`<url—pattern>*.jsp</url-pattern>`过滤所有对jsp文件的访问请求。

4、作用于某一文件夹下某一类型文件：`<url—pattern>/dir/*.扩展名</url-pattern>`

如果一个过滤器需要过滤多种文件，则可以配置多个`<filter-mapping>`，一个mapping定义一个url-pattern来定义过滤规则。如下：

```xml
<filter>
  <filter-name>loginFilter</filter-name>
  <filter-class>com.nnngu.filter.loginFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>loginFilter</filter-name>
  <url-pattern>*.jsp</url-pattern>
</filter-mapping>
<filter-mapping>
  <filter-name>loginFilter</filter-name>
  <url-pattern>*.do</url-pattern>
</filter-mapping>
```

### 例1：用过滤器实现登录验证，没登录则驳回访问请求并重定向到登录页面。

```java
public void doFilter(ServletRequest arg0, ServletResponse arg1,
            FilterChain arg2) throws IOException, ServletException {
        
        HttpServletRequest request=(HttpServletRequest) arg0;
        HttpServletResponse response=(HttpServletResponse) arg1;
        HttpSession session=request.getSession();
        
        String path=request.getRequestURI();
        
        Integer uid=(Integer)session.getAttribute("userid");
        
        if(path.indexOf("/login.jsp")>-1){//登录页面不过滤
            arg2.doFilter(arg0, arg1);//递交给下一个过滤器
            return;
        }
        if(path.indexOf("/register.jsp")>-1){//注册页面不过滤
            arg2.doFilter(request, response);
            return;
        }
        
        if(uid!=null){//已经登录
            arg2.doFilter(request, response);//放行，递交给下一个过滤器
            
        }else{
            response.sendRedirect("login.jsp");
        }

    }
```

### 例2：设置字符编码

```java
public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {
          HttpServletRequest request2=(HttpServletRequest) request;
          HttpServletResponse response2=(HttpServletResponse) response;
          
          request2.setCharacterEncoding("UTF-8");  
          response2.setCharacterEncoding("UTF-8"); 
          
          chain.doFilter(request, response); 

    }
```





















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-Java%E8%BF%87%E6%BB%A4%E5%99%A8Filter%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-Java%E8%BF%87%E6%BB%A4%E5%99%A8Filter%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/3/22/1521719259966.jpg