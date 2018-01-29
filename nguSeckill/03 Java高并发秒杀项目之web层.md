# 03 Java高并发秒杀项目之web层
Github：https://github.com/nnngu  
项目源代码：https://github.com/nnngu/nguSeckill

---

本篇是`Web`层，所以首先要先引入`SpringMvc`

修改`web.xml`，引入`SpringMvc`的`DispatcherServlet`：

![][1]

`web.xml`里面的代码请参照项目的源代码。

## 接下来编写 SeckillController 

创建`SeckillController.java`，如下图：

![][2]

`SeckillController.java`里面的代码请参照项目的源代码。

## 建立一个全局ajax请求返回类，返回json

创建`SeckillResult.java`，如下图：

![][3]

`SeckillResult.java`里面的代码请参照项目的源代码。

## 页面的编写

项目的前端页面是由`Bootstrap`开发的，所以我们要先去下载`Bootstrap`或者是使用在线CDN。

使用在线CDN的方法：

```html
<!-- Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

<!-- 可选的 Bootstrap 主题文件（一般不用引入） -->
<link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">

<!-- Bootstrap 核心 JavaScript 文件 -->
<script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

为了方便我们本地调试，我在项目里使用的是本地的`Bootstrap`。

### 步骤：

1. 下载`JQuery`，因为`Bootstrap`就是依赖`JQuery`的

2. 下载`Bootstrap`

3. 下载一个倒计时插件`jquery.countdown.min.js` ，再下载一个操作`Cookie`插件`jquery.cookie.min.js` 如图放置：

![][4]

4. 编写一个公共的头部`jsp`文件，位于`WEB-INF/jsp/common`下的`head.jsp`，如下图：

![][5]

`head.jsp`里面的代码请参照项目的源代码。

5. 编写一个公共的`jstl`标签库文件`tag.jsp`，在下图所示的位置。

![][6]

`tag.jsp`里面的代码请参照项目的源代码。

6. 编写列表页面`list.jsp`，在下图所示的位置。

![][7]


7. 编写秒杀详情页面`detail.jsp`，在下图所示的位置。

![][8]

`detail.jsp`里面的代码请参照项目的源代码。

## 添加 applicationContext-web.xml

添加 `applicationContext-web.xml`，在下图所示的位置。

![][9]

 `applicationContext-web.xml`里面的代码请参照项目的源代码。
 
 ## 添加 seckill.js 文件
 
 添加 `seckill.js` 文件，在下图所示的位置。
 
 ![][10]
 
 `seckill.js` 里面的代码请参照项目的源代码。
 
 ## 运行项目
 
 运行项目，部署到`tomcat`，在浏览器地址栏输入 `http://localhost:8080/seckill/list`，敲回车，即可看到如下图的界面：
 
 ![][11]
 
 到此，我们成功完成了web层的开发。

### 下一篇：[04 Java高并发秒杀项目之高并发优化](https://github.com/nnngu/LearningNotes/blob/master/nguSeckill/04%20Java高并发秒杀项目之高并发优化.md)
 
 
 
 
 
 


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517174370108.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517175668689.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517175867220.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517177850911.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517178335741.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517179505188.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517179663648.jpg
  [8]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517180009140.jpg
  [9]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517182288815.jpg
  [10]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517183946959.jpg
  [11]: https://www.github.com/nnngu/FigureBed/raw/master/2018/1/29/1517184316644.jpg