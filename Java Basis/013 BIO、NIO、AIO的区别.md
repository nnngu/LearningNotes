# 013 BIO、NIO、AIO的区别
作者：[nnngu](https://github.com/nnngu)  
GitHub：[https://github.com/nnngu](https://github.com/nnngu)  
博客园：[http://www.cnblogs.com/nnngu](http://www.cnblogs.com/nnngu/)  
简书：[https://www.jianshu.com/users/1df20d76ea5c](https://www.jianshu.com/users/1df20d76ea5c)  
知乎：[https://www.zhihu.com/people/nnngu/posts](https://www.zhihu.com/people/nnngu/posts)  

---

 IO通常分为几种，BIO（阻塞 Blocking IO）、NIO（非阻塞 Non-Blocking IO）、AIO（异步非阻塞）。

## BIO

在JDK1.4出来之前，我们建立网络连接的时候采用BIO模式，需要先在服务端启动一个ServerSocket，然后在客户端启动Socket来对服务端进行通信，默认情况下服务端需要对每个请求建立一堆线程等待请求，而客户端发送请求后，先询问服务端是否有线程响应，如果没有则会一直等待或者遭到拒绝请求。BIO模型图如下：

![][1]

### BIO优缺点
* 优点
	* 模型简单
	* 编码简单
* 缺点
	* 性能瓶颈低

优缺点很明显。这里主要说下缺点：主要瓶颈在线程上。每个连接都会建立一个线程。虽然线程消耗比进程小，但是一台机器实际上能建立的有效线程有限，以Java来说，1.5以后，一个线程大致消耗1M内存！且随着线程数量的增加，CPU切换线程上下文的消耗也随之增加，在高过某个阀值后，继续增加线程，性能不增反降！而同样因为一个连接就新建一个线程，所以编码模型很简单！

就性能瓶颈这一点，就确定了BIO并不适合进行高性能服务器的开发！像Tomcat这样的Web服务器，从7开始就从BIO改成了NIO，来提高服务器性能！

## NIO

NIO本身是基于事件驱动思想来完成的，其主要想解决的是BIO的大并发问题：在使用同步I/O的网络应用中，如果要同时处理多个客户端请求，就必须使用多线程来处理。也就是说，将每一个客户端请求分配给一个线程来单独处理。这样做虽然可以达到我们的要求，但同时又会带来另外一个问题。由于每创建一个线程，就要为这个线程分配一定的内存空间（也叫工作存储器），而且操作系统本身也对线程的总数有一定的限制。如果客户端过多，服务端程序可能会因为不堪重负而拒绝客户端的请求，甚至服务器可能会因此而瘫痪。

NIO基于Reactor，当socket有流可读或可写入socket时，操作系统会相应的通知应用程序进行处理，应用再将流读取到缓冲区或写入操作系统。  也就是说，这个时候，已经不是一个连接就要对应一个处理线程了，而是有效的请求，对应一个线程，当连接没有数据时，是没有工作线程来处理的。

BIO与NIO一个比较重要的不同，是我们使用BIO的时候往往会引入多线程，每个连接一个单独的线程；而NIO则是使用单线程或者只使用少量的多线程。NIO模型图如下：

![][2]

### NIO优缺点
* 优点
	* 性能瓶颈高
* 缺点
	* 模型复杂
	* 编码复杂
	* 需处理半包问题

NIO的优缺点和BIO就完全相反了！性能高，不用一个连接就建一个线程，可以一个线程处理所有的连接！相应的，编码就复杂很多。还有一个问题，由于是非阻塞的，应用无法知道什么时候消息读完！

BIO和NIO的对比图：

![][3]

## AIO

AIO没有前两者普及，暂不讨论！


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/25/1516814717421.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/25/1516814803100.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/25/1516812993887.jpg