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























---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-Java%E7%9B%91%E5%90%AC%E5%99%A8Listener%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md](https://github.com/nnngu/LearningNotes/blob/master/_posts/2018-03-22-Java%E7%9B%91%E5%90%AC%E5%99%A8Listener%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3.md)