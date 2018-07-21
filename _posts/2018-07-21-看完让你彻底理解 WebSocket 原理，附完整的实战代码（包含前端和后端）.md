---
categories: WebSocket
description: 最近有同学问我有没有做过在线咨询功能。同时，公司也刚好让我接手一个 IM 项目。所以今天抽时间记录一下最近学习的内容。本文主要剖析了 WebSocket 的原理，以及附上一个完整的聊天室实战 Demo （包含前端和后端，下载链接在文末）。
---

## 1、前言

最近有同学问我有没有做过在线咨询功能。同时，公司也刚好让我接手一个 IM 项目。所以今天抽时间记录一下最近学习的内容。本文主要剖析了 WebSocket 的原理，以及附上一个完整的聊天室实战 Demo （包含前端和后端，下载链接在文末）。

## 2、WebSocket 与 HTTP

WebSocket 协议在2008年诞生，2011年成为国际标准。现在所有浏览器都已经支持了。WebSocket 的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话。

HTTP 有 1.1 和 1.0 之说，也就是所谓的 keep-alive ，把多个 HTTP 请求合并为一个，但是 Websocket 其实是一个新协议，跟 HTTP 协议基本没有关系，只是为了兼容现有浏览器，所以在握手阶段使用了 HTTP 。

下面一张图说明了 HTTP 与 WebSocket 的主要区别：

![][1]

WebSocket 的其他特点：

- 建立在 TCP 协议之上，服务器端的实现比较容易。
- 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
- 数据格式比较轻量，性能开销小，通信高效。
- 可以发送文本，也可以发送二进制数据。
- 没有同源限制，客户端可以与任意服务器通信。
- 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。

## 3、WebSocket 是什么样的协议，具体有什么优点

首先，WebSocket 是一个持久化的协议，相对于 HTTP 这种非持久的协议来说。简单的举个例子吧，用目前应用比较广泛的 PHP 生命周期来解释。

HTTP 的生命周期通过 Request 来界定，也就是一个 Request 一个 Response ，那么在 HTTP1.0 中，这次 HTTP 请求就结束了。

在 HTTP1.1 中进行了改进，使得有一个 keep-alive，也就是说，在一个 HTTP 连接中，可以发送多个 Request，接收多个 Response。但是请记住 Request = Response， 在 HTTP 中永远是这样，也就是说一个 Request 只能有一个 Response。而且这个 Response 也是被动的，不能主动发起。

你 BB 了这么多，跟 WebSocket 有什么关系呢？ 好吧，我正准备说 WebSocket 呢。

首先 WebSocket 是基于 HTTP 协议的，或者说借用了 HTTP 协议来完成一部分握手。

首先我们来看个典型的 WebSocket 握手

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```

熟悉 HTTP 的童鞋可能发现了，这段类似 HTTP 协议的握手请求中，多了这么几个东西。

```
Upgrade: websocket
Connection: Upgrade
```

这个就是 WebSocket 的核心了，告诉 Apache 、 Nginx 等服务器：注意啦，我发起的请求要用 WebSocket 协议，快点帮我找到对应的助理处理~而不是那个老土的 HTTP。

```
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

首先， Sec-WebSocket-Key 是一个 Base64 encode 的值，这个是浏览器随机生成的，告诉服务器：泥煤，不要忽悠我，我要验证你是不是真的是 WebSocket 助理。

然后， Sec_WebSocket-Protocol 是一个用户定义的字符串，用来区分同 URL 下，不同的服务所需要的协议。简单理解：今晚我要服务A，别搞错啦~

最后， Sec-WebSocket-Version 是告诉服务器所使用的 WebSocket Draft （协议版本），在最初的时候，WebSocket 协议还在 Draft 阶段，各种奇奇怪怪的协议都有，而且还有很多期奇奇怪怪不同的东西，什么 Firefox 和 Chrome 用的不是一个版本之类的，当初 WebSocket 协议太多可是一个大难题。。不过现在还好，已经定下来啦~大家都使用同一个版本： 服务员，我要的是13岁的噢→_→

然后服务器会返回下列东西，表示已经接受到请求， 成功建立 WebSocket 啦！

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```

这里开始就是 HTTP 最后负责的区域了，告诉客户，我已经成功切换协议啦~

```
Upgrade: websocket
Connection: Upgrade
```

依然是固定的，告诉客户端即将升级的是 WebSocket 协议，而不是 mozillasocket，lurnarsocket 或者 shitsocket。

然后， Sec-WebSocket-Accept 这个则是经过服务器确认，并且加密过后的 Sec-WebSocket-Key 。 服务器：好啦好啦，知道啦，给你看我的 ID CARD 来证明行了吧。

后面的， Sec-WebSocket-Protocol 则是表示最终使用的协议。

至此，HTTP 已经完成它所有工作了，接下来就是完全按照 WebSocket 协议进行了。

## 4、WebSocket 的作用

在讲 WebSocket之前，我就顺带着讲下 ajax轮询 和 long poll 的原理。

### 4-1、ajax轮询

ajax轮询的原理非常简单，让浏览器隔个几秒就发送一次请求，询问服务器是否有新信息。

**场景再现：**

```
客户端：啦啦啦，有没有新信息(Request)

服务端：没有（Response）

客户端：啦啦啦，有没有新信息(Request)

服务端：没有。。（Response）

客户端：啦啦啦，有没有新信息(Request)

服务端：你好烦啊，没有啊。。（Response）

客户端：啦啦啦，有没有新消息（Request）

服务端：好啦好啦，有啦给你。（Response）

客户端：啦啦啦，有没有新消息（Request）

服务端：。。。。。没。。。。没。。。没有（Response） —- loop
```

### 4-2、long poll

long poll 其实原理跟 ajax轮询 差不多，都是采用轮询的方式，不过采取的是阻塞模型（一直打电话，没收到就不挂电话），也就是说，客户端发起请求后，如果没消息，就一直不返回 Response 给客户端。直到有消息才返回，返回完之后，客户端再次建立连接，周而复始。

**场景再现：**

```
客户端：啦啦啦，有没有新信息，没有的话就等有了才返回给我吧（Request）

服务端：额。。 等待到有消息的时候。。来 给你（Response）

客户端：啦啦啦，有没有新信息，没有的话就等有了才返回给我吧（Request） -loop
```

**从上面可以看出其实这两种方式，都是在不断地建立HTTP连接，然后等待服务端处理，可以体现HTTP协议的另外一个特点，被动性。**

何为被动性呢，其实就是，服务端不能主动联系客户端，只能有客户端发起。

从上面很容易看出来，不管怎么样，上面这两种都是非常消耗资源的。

ajax轮询 需要服务器有很快的处理速度和资源。long poll 需要有很高的并发，也就是说同时接待客户的能力。

所以 ajax轮询 和 long poll 都有可能发生这种情况。

```

客户端：啦啦啦啦，有新信息么？

服务端：正忙，请稍后再试（503 Server Unavailable）

客户端：。。。。好吧，啦啦啦，有新信息么？

服务端：正忙，请稍后再试（503 Server Unavailable）

```

### 4-3、WebSocket

通过上面这两个例子，我们可以看出，这两种方式都不是最好的方式，需要很多资源。

一种需要更快的速度，一种需要更多的’电话’。这两种都会导致’电话’的需求越来越高。

哦对了，忘记说了 HTTP 还是一个无状态协议。通俗的说就是，服务器因为每天要接待太多客户了，是个健忘鬼，你一挂电话，他就把你的东西全忘光了，把你的东西全丢掉了。你第二次还得再告诉服务器一遍。

所以在这种情况下出现了 WebSocket 。他解决了 HTTP 的这几个难题。首先，被动性，当服务器完成协议升级后（HTTP->Websocket），服务端就可以主动推送信息给客户端啦。所以上面的情景可以做如下修改。

```

客户端：啦啦啦，我要建立Websocket协议，需要的服务：chat，Websocket协议版本：17（HTTP Request）

服务端：ok，确认，已升级为Websocket协议（HTTP Protocols Switched）

客户端：麻烦你有信息的时候推送给我噢。。

服务端：ok，有的时候会告诉你的。

服务端：balabalabalabala

服务端：balabalabalabala

服务端：哈哈哈哈哈啊哈哈哈哈

服务端：笑死我了哈哈哈哈哈哈哈

```

这样，只需要经过一次 HTTP 请求，就可以做到源源不断的信息传送了。

## 5、实战代码

前端代码：[https://github.com/nnngu/WebSocketDemo-js](https://github.com/nnngu/WebSocketDemo-js)      
后端代码：[https://github.com/nnngu/WebSocketDemo-php](https://github.com/nnngu/WebSocketDemo-php)  

运行步骤：

1. 在终端打开 `WebSocketDemo-php` 目录，执行 `php -q server.php`
2. 用浏览器访问 `WebSocketDemo-js` 目录里面的 `index.html`

运行截图：

![][2]

参考文档：   
[php socket 文档](http://php.net/manual/zh/ref.sockets.php)      
[前端 WebSocket 文档](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)   




  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/7/21/1532166645819.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/7/21/1532169032942.jpg