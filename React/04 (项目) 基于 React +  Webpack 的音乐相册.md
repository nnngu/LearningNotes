# 04 (项目) 基于 React +  Webpack 的音乐相册

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

[上一篇文章用爬虫自动下载了一些图片](https://github.com/nnngu/LearningNotes/blob/master/Spider/02%20Python%E7%88%AC%E8%99%AB%E5%AE%9E%E7%8E%B0%E7%99%BE%E5%BA%A6%E5%9B%BE%E7%89%87%E8%87%AA%E5%8A%A8%E4%B8%8B%E8%BD%BD.md)，这一篇就用这些图片做一个音乐相册吧！

## 效果预览

![][1]

点击图片，切换到背面：

![][2]

### 演示地址

演示地址：[https://nnngu.github.io/MusicPhoto/](https://nnngu.github.io/MusicPhoto/)

## 环境搭建

1、安装 npm，安装成功后，在终端输入 `npm -v` 可以查看它的版本。

![][3]

2、安装`generator-react-webpack`，使用如下命令：

```
npm install -g generator-react-webpack
```

安装完成之后，输入`npm list --depth=0 -global` 可以查看版本。

![][4]

3、创建项目，打开你的项目目录，然后输入：`yo react-webpack MusicPhoto`

4、项目的目录如下图：

![][5]

需要注意的几个地方：

* ① cfg 目录是配置文件所在的目录
  * 重点关注 cfg 目录里面的 defaults.js 文件  
* ② src 项目的源代码主要在这里面
* ③ package.json 用来管理和配置依赖模块

## 添加 autoprefixer-loader 模块

autoprefixer-loader 是用来处理 css 的模块，安装命令：

```
npm install autoprefixer-loader --save-dev
```

然后打开 cfg 目录中的 defaults.js 添加如下配置信息：

![][6]

## 添加 json-loader 模块

json-loader 是用来处理 json 的模块，安装命令：

```
npm install json-loader --save-dev
```

然后打开 cfg 目录中的 defaults.js 添加如下配置信息：

![][7]




  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/5/1517842690437.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/5/1517842775081.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517848578071.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517848855856.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517849337904.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517850101903.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517850270658.jpg