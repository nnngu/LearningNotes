---
categories: React
description: 这一篇就用这些图片做一个音乐相册吧！
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

3、创建项目，打开你用来存放代码的目录，然后输入：`yo react-webpack MusicPhoto`

4、创建完成，项目的目录如下图：

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

## 添加图片

1、在 src 目录下添加 images 目录和一些图片，如下图：(图片尺寸全部是 260 \* 260) 

![][8]

2、添加 imageDatas.json 如下图：

![][9]

imageDatas.json 里面的代码请参照项目的源代码。

3、在`src/components/Main.js`中引入`imageDatas.json` 代码如下：

```javascript
// 获取图片的 json 数据
var imagesData = require('../data/imageDatas.json');
```

4、根据图片的文件名，生成图片URL。

*src/components/Main.js*

```javascript
/**
 * @imagesDataArray  {Array}
 * @return {Array}
 */
imagesData = (function getImageURL(imagesDataArray) {
  for (var i = 0, j = imagesDataArray.length; i < j; i++) {
    var singleImageData = imagesDataArray[i];

    singleImageData.imageURL = require('../images/' + singleImageData.fileName);

    imagesDataArray[i] = singleImageData;
  }
  return imagesDataArray;
})(imagesData);
```

## 配置字体

打开 cfg 目录中的 defaults.js 添加如下配置信息：

![][10]

## 组件的绑定

src/index.html 中的关键代码：

![][11]

src/index.js 中的关键代码：

![][12]

## 代码逻辑

主要的代码逻辑在 `Main.js`中，主要的布局样式在 `App.scss`中。如下图：

![][13]

具体代码请参照项目的源代码 [https://github.com/nnngu/MusicPhoto](https://github.com/nnngu/MusicPhoto)

## 发布到Github Pages

1、修改`cfg/defaults.js`中的`publicPath`，改为`publicPath: './assets/',` 如下图：

![][14]

2、打包，使用`npm run dist`指令。打包完成可以看到如下目录：

![][15]

3、把打包好的目录 push 到 GitHub 的 gh-pages 分支，使用如下命令：

```
git subtree push --prefix=dist origin gh-pages
```

4、在GitHub 对应的仓库里面开启 Github Pages 功能，并选择 `gh-pages`分支即可。

**👇👇👇下一篇将会总结完成音乐播放器的过程。👇👇👇**

[05 (项目) 基于 React + Webpack 的音乐相册（下）](https://github.com/nnngu/LearningNotes/blob/master/React/05%20(%E9%A1%B9%E7%9B%AE)%20%E5%9F%BA%E4%BA%8E%20React%20%2B%20Webpack%20%E7%9A%84%E9%9F%B3%E4%B9%90%E7%9B%B8%E5%86%8C%EF%BC%88%E4%B8%8B%EF%BC%89.md)



  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/5/1517842690437.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/5/1517842775081.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517848578071.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517848855856.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517849337904.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517850101903.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517850270658.jpg
  [8]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517851778975.jpg
  [9]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517851939423.jpg
  [10]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517852817008.jpg
  [11]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517853041622.jpg
  [12]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517853081657.jpg
  [13]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517853295536.jpg
  [14]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517853662271.jpg
  [15]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/6/1517853876038.jpg
