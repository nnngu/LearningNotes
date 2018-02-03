# 02 Python爬虫实现百度图片自动下载

Github：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)

---

## 制作爬虫的步骤

制作一个爬虫一般分以下几个步骤：

* 分析需求
* 分析网页源代码，配合开发者工具
* 编写正则表达式或者XPath表达式
* 正式编写 python 爬虫代码

## 效果预览

运行效果如下：

![][1]

存放图片的文件夹：

![][2]

## 需求分析

我们的爬虫至少要实现两个功能：一是搜索图片，二是自动下载。

搜索图片：最容易想到的是爬百度图片的结果，我们就上百度图片看看：

![][3]

随便搜索几个关键字，可以看到已经搜索出来很多张图片：

![][4]

## 分析网页

我们点击右键，查看源代码：

![][5]

打开源代码之后，发现一堆源代码比较难找出我们想要的资源。

这个时候，就要用开发者工具！我们回到上一页面，调出开发者工具，我们需要用的是左上角那个东西：(鼠标跟随)。

![][6]

然后选择你想看源代码的地方，就可以发现，下面的代码区自动定位到了相应的位置。如下图：

![][7]

![][8]

我们复制这个地址，然后到刚才的一堆源代码里搜索一下，发现了它的位置，但是这里我们又疑惑了，这个图片有这么多地址，到底用哪个呢？我们可以看到有thumbURL，middleURL，hoverURL，objURL

![][9]

通过分析可以知道，前面两个是缩小的版本，hoverURL 是鼠标移动过后显示的版本，objURL 应该是我们需要的，可以分别打开这几个网址看看，发现 objURL 的那个最大最清晰。

找到了图片地址，接下来我们分析源代码。看看是不是所有的 objURL 都是图片。

![][10]

发现都是以.jpg格式结尾的图片。

## 编写正则表达式

```python
pic_url = re.findall('"objURL":"(.*?)",',html,re.S)
```

## 编写爬虫代码

这里我们用了2个包，一个是正则，一个是 requests 包

```python
#-*- coding:utf-8 -*-
import re
import requests
```

复制百度图片搜索的链接，传入 requests ，然后把正则表达式写好

![][11]

```python
url = 'https://image.baidu.com/search/index?tn=baiduimage&ie=utf-8&word=%E6%A0%97%E5%B1%B1%E6%9C%AA%E6%9D%A5%E5%A4%B4%E5%83%8F&ct=201326592&ic=0&lm=-1&width=&height=&v=index'

html = requests.get(url).text
pic_url = re.findall('"objURL":"(.*?)",',html,re.S)

```

因为有很多张图片，所以要循环，我们打印出结果来看看，然后用 requests 获取网址，由于有些图片可能存在网址打不开的情况，所以加了10秒超时控制。

```python
pic_url = re.findall('"objURL":"(.*?)",',html,re.S)
i = 1
for each in pic_url:
    print each
    try:
        pic= requests.get(each, timeout=10)
    except requests.exceptions.ConnectionError:
        print('【错误】当前图片无法下载')
        continue

```

接着就是把图片保存下来，我们事先建立好一个 images 目录，把图片都放进去，命名的时候，以数字命名。

```python
        dir = '../images/' + keyword + '_' + str(i) + '.jpg'
        fp = open(dir, 'wb')
        fp.write(pic.content)
        fp.close()
        i += 1
```

## 完整的代码

```python
# -*- coding:utf-8 -*-
import re
import requests


def dowmloadPic(html, keyword):
    pic_url = re.findall('"objURL":"(.*?)",', html, re.S)
    i = 1
    print('找到关键词:' + keyword + '的图片，现在开始下载图片...')
    for each in pic_url:
        print('正在下载第' + str(i) + '张图片，图片地址:' + str(each))
        try:
            pic = requests.get(each, timeout=10)
        except requests.exceptions.ConnectionError:
            print('【错误】当前图片无法下载')
            continue

        dir = '../images/' + keyword + '_' + str(i) + '.jpg'
        fp = open(dir, 'wb')
        fp.write(pic.content)
        fp.close()
        i += 1


if __name__ == '__main__':
    word = input("Input key word: ")
    url = 'http://image.baidu.com/search/flip?tn=baiduimage&ie=utf-8&word=' + word + '&ct=201326592&v=flip'
    result = requests.get(url)
    dowmloadPic(result.text, word)

```

![][12]

![][13]

我们看到有的图片没显示出来，打开网址看，发现确实没了。

![][14]

因为百度有些图片它缓存到百度的服务器上，所以我们在百度上还能看见它，但它的实际链接已经失效了。

## 总结

enjoy 我们的第一个图片下载爬虫吧！当然它不仅能下载百度的图片，依葫芦画瓢，你现在应该能做很多事情了，比如爬取头像，爬淘宝展示图等等。


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517624440357.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517624588214.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517624851741.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517625097976.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517625636570.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517626066422.jpg
  [7]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517626276983.jpg
  [8]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517626329451.jpg
  [9]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517626739154.jpg
  [10]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517627100214.jpg
  [11]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517627638515.jpg
  [12]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517629256979.jpg
  [13]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517629346426.jpg
  [14]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/3/1517629377850.jpg