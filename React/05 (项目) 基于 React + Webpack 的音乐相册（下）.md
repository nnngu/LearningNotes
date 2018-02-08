# 05 (项目) 基于 React + Webpack 的音乐相册（下）

笔记仓库：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)    

---

[上一篇完成了音乐相册里面的相册功能](https://github.com/nnngu/LearningNotes/blob/master/React/04%20(%E9%A1%B9%E7%9B%AE)%20%E5%9F%BA%E4%BA%8E%20React%20%2B%20%20Webpack%20%E7%9A%84%E9%9F%B3%E4%B9%90%E7%9B%B8%E5%86%8C%EF%BC%88%E4%B8%8A%EF%BC%89.md)，这一篇主要总结的是音乐相册里面的音乐播放器功能。

## 数据准备

在`src/data`目录添加音乐数据文件：`musicDatas.js`

代码如下：

```javascript
export const MUSIC_LIST = [
  {
    id: 1,
    title: '童话镇',
    artist: '陈一发儿',
    file: 'https://raw.githubusercontent.com/nnngu/SharedResource/master/music/%E7%AB%A5%E8%AF%9D%E9%95%87.mp3',
    cover: 'https://raw.githubusercontent.com/nnngu/FigureBed/master/2018/2/6/tong_hua_zhen.jpg'
  }, {
    id: 2,
    title: '天使中的魔鬼',
    artist: '田馥甄',
    file: 'http://oj4t8z2d5.bkt.clouddn.com/%E9%AD%94%E9%AC%BC%E4%B8%AD%E7%9A%84%E5%A4%A9%E4%BD%BF.mp3',
    cover: 'http://oj4t8z2d5.bkt.clouddn.com/%E9%AD%94%E9%AC%BC%E4%B8%AD%E7%9A%84%E5%A4%A9%E4%BD%BF.jpg'
  }, {
    id: 3,
    title: '风继续吹',
    artist: '张国荣',
    file: 'http://oj4t8z2d5.bkt.clouddn.com/%E9%A3%8E%E7%BB%A7%E7%BB%AD%E5%90%B9.mp3',
    cover: 'http://oj4t8z2d5.bkt.clouddn.com/%E9%A3%8E%E7%BB%A7%E7%BB%AD%E5%90%B9.jpg'
  }, {
    id: 4,
    title: '恋恋风尘',
    artist: '老狼',
    file: 'http://oj4t8z2d5.bkt.clouddn.com/%E6%81%8B%E6%81%8B%E9%A3%8E%E5%B0%98.mp3',
    cover: 'http://oj4t8z2d5.bkt.clouddn.com/%E6%81%8B%E6%81%8B%E9%A3%8E%E5%B0%98.jpg'
  }, {
    id: 5,
    title: '我要你',
    artist: '任素汐',
    file: 'http://oj4t8z2d5.bkt.clouddn.com/%E6%88%91%E8%A6%81%E4%BD%A0.mp3',
    cover: 'http://oj4t8z2d5.bkt.clouddn.com/%E6%88%91%E8%A6%81%E4%BD%A0.jpg'
  }, {
    id: 6,
    title: '成都',
    artist: '赵雷',
    file: 'http://oj4t8z2d5.bkt.clouddn.com/%E6%88%90%E9%83%BD.mp3',
    cover: 'http://oj4t8z2d5.bkt.clouddn.com/%E6%88%90%E9%83%BD.jpg'
  }, {
    id: 7,
    title: 'sound of silence',
    artist: 'Simon & Garfunkel',
    file: 'http://oj4t8z2d5.bkt.clouddn.com/sound-of-silence.mp3',
    cover: 'http://oj4t8z2d5.bkt.clouddn.com/sound-of-silence.jpg'
  }

];

```

## 进度条功能

1、在`src/index.html`文件中添加一个div，作为jPlayer（音乐播放插件）的容器。如下图红色框里面的就是新添加的代码：

![][1]

2、继续在`src/index.html`文件中应用 jQuery 和 jPlayer 。

![][2]

3、添加进度条组件：在`src/components/music` 目录添加 `progress.js`，如下图：

![][3]

`progress.js`的代码如下：

```javascript
import React from 'react';

require('./progress.less');

let Progress = React.createClass({

  getDefaultProps() {
    return {
      barColor: '#2f9842'
    }
  },

  changeProgress(e) {
    let progressBar = this.refs.progressBar;
    let progress = (e.clientX - progressBar.getBoundingClientRect().left) / progressBar.clientWidth;
    this.props.onProgressChange && this.props.onProgressChange(progress);
  },

  render() {
    return (
      <div className="components-progress" ref="progressBar" onClick={this.changeProgress}>
        <div className="progress" style={{width: `${this.props.progress}%`, background: this.props.barColor}}></div>
      </div>
    );
  }
});

export default Progress;

```

在同一个目录下创建Progress 的样式文件 `progress.less`，代码如下：

```css
.components-progress {
	display: inline-block;
	width: 100%;
	height: 3px;
	position: relative;
	background: #aaa;
	cursor: pointer;

	.progress {
		width: 0%;
		height: 3px;
		left: 0;
		top: 0;
	}
}

```






  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518121965642.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518122145709.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518122335010.jpg