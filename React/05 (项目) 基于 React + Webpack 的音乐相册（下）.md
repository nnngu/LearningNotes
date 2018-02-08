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

  // ......
  
  // 省略了一部分代码
  // 完整的代码请参照项目的源代码
  
});

export default Progress;

```

在同一个目录下创建Progress 的样式文件 `progress.less`，代码如下：

```less
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

## 创建播放器组件

播放器组件分别对应`player.js` 和 `player.less` 两个文件。如下图：

![][4]

`player.js` 的代码如下：

```javascript
import React from 'react';
import Progress from './progress';
import {MUSIC_LIST} from '../../data/musicDatas';

let PubSub = require('pubsub-js');
require('./player.less');

let duration = null;

let Player = React.createClass({

  /**
   * 生命周期方法 componentDidMount
   */
  componentDidMount() {
    $('#player').bind($.jPlayer.event.timeupdate, (e) => {
      duration = e.jPlayer.status.duration;
      this.setState({
        progress: e.jPlayer.status.currentPercentAbsolute,
        volume: e.jPlayer.options.volume * 100,
        leftTime: this.formatTime(duration * (1 - e.jPlayer.status.currentPercentAbsolute / 100))
      });
    });

    $('#player').bind($.jPlayer.event.ended, (e) => {
      this.playNext();
    });
  },

  /**
   * 生命周期方法 componentWillUnmount
   */
  componentWillUnmount() {
    $('#player').unbind($.jPlayer.event.timeupdate);
  },

  formatTime(time) {
    time = Math.floor(time);
    let miniute = Math.floor(time / 60);
    let seconds = Math.floor(time % 60);

    return miniute + ':' + (seconds < 10 ? '0' + seconds : seconds);
  },

  /**
   * 进度条被拖动的处理方法
   * @param progress
   */
  changeProgressHandler(progress) {
    $('#player').jPlayer('play', duration * progress);
    this.setState({
      isPlay: true
    });

    // 获取转圈的封面图片
    let imgAnimation = this.refs.imgAnimation;
    imgAnimation.style = 'animation-play-state: running';
  },

  /**
   * 音量条被拖动的处理方法
   * @param progress
   */
  changeVolumeHandler(progress) {
    $('#player').jPlayer('volume', progress);
  },

  /**
   * 播放或者暂停方法
   **/
  play() {
    this.setState({
      isPlay: !this.state.isPlay
    });

    // 获取转圈的封面图片
    var imgAnimation = this.refs.imgAnimation;

    if (this.state.isPlay) {
      $('#player').jPlayer('pause');
      imgAnimation.style = 'animation-play-state: paused';
    } else {
      $('#player').jPlayer('play');
      imgAnimation.style = 'animation-play-state: running';
    }

  },

  /**
   * 下一首
   **/
  next() {
    PubSub.publish('PLAY_NEXT');

    this.setState({
      isPlay: true
    });

    // 开始播放下一首
    this.playNext();

    // 获取转圈的封面图片
    let imgAnimation = this.refs.imgAnimation;
    imgAnimation.style = 'animation-play-state: running';

  },

  /**
   * 上一首
   **/
  prev() {
    PubSub.publish('PLAY_PREV');

    this.setState({
      isPlay: true
    });

    // 开始播放上一首
    this.playNext('prev');

    // 获取转圈的封面图片
    let imgAnimation = this.refs.imgAnimation;
    imgAnimation.style = 'animation-play-state: running';

  },

  playNext(type = 'next') {
    let index = this.findMusicIndex(this.state.currentMusitItem);
    if (type === 'next') {
      index = (index + 1) % this.state.musicList.length;
    } else {
      index = (index + this.state.musicList.length - 1) % this.state.musicList.length;
    }
    let musicItem = this.state.musicList[index];
    this.setState({
      currentMusitItem: musicItem
    });
    this.playMusic(musicItem);
  },

  playMusic(item) {
    $('#player').jPlayer('setMedia', {
      mp3: item.file
    }).jPlayer('play');
    this.setState({
      currentMusitItem: item
    });
  },

  findMusicIndex(music) {
    let index = this.state.musicList.indexOf(music);
    return Math.max(0, index);
  },

  changeRepeat() {
    PubSub.publish('CHANAGE_REPEAT');
  },

  // constructor() {
  //   return {
  //     progress: 0,
  //     volume: 0,
  //     isPlay: true,
  //     leftTime: ''
  //   }
  // },

  componentWillMount() {
    this.getInitialState();
  },

  getInitialState() {
    return {
      musicList: MUSIC_LIST,
      currentMusitItem: MUSIC_LIST[0],
      repeatType: 'cycle',

      progress: 0,
      volume: 0,
      isPlay: true,
      leftTime: ''
    }
  },

  /**
   * render 渲染方法
   * @returns {*}
   */
  render() {
    return (
      <div className="player-page">
        <div className=" row">
          <div className="controll-wrapper">
            <h2 className="music-title">{this.state.currentMusitItem.title}</h2>
            <h3 className="music-artist mt10">{this.state.currentMusitItem.artist}</h3>
            <div className="row mt10">
              <div className="left-time -col-auto">-{this.state.leftTime}</div>
              <div className="volume-container">
                <i className="icon-volume rt" style={{top: 5, left: -5}}></i>
                <div className="volume-wrapper">

                  {/* 音量条 */}
                  <Progress
                    progress={this.state.volume}
                    onProgressChange={this.changeVolumeHandler}
                    // barColor='#aaa'
                  >
                  </Progress>
                </div>
              </div>
            </div>
            <div style={{height: 10, lineHeight: '10px'}}>

              {/* 播放进度条 */}
              <Progress
                progress={this.state.progress}
                onProgressChange={this.changeProgressHandler}
              >
              </Progress>
            </div>
            <div className="mt35 row">
              <div>
                <i className="icon prev" onClick={this.prev}></i>
                <i className={`icon ml20 ${this.state.isPlay ? 'pause' : 'play'}`} onClick={this.play}></i>
                <i className="icon next ml20" onClick={this.next}></i>
              </div>
              <div className="-col-auto">
                {/* 播放模式按钮：单曲、循环、随机 */}
                <i className={`repeat-${this.state.repeatType}`} onClick={this.changeRepeat}></i>
              </div>
            </div>
          </div>
          <div className="-col-auto cover">
            <img ref="imgAnimation" src={this.state.currentMusitItem.cover} alt={this.state.currentMusitItem.title}/>
          </div>
        </div>
      </div>
    );
  }
});

export default Player;

```

`player.less` 的代码如下：

```less
.player-page {
	width: 550px;
  height: 210px;
	//margin: auto;
	//margin-top: 0px;

  position: absolute;
  left: 50%;
  transform: translate(-50%, 0);
  bottom: 20px;
  z-index: 101;
  //width: 100%;

	//.caption {
	//	font-size: 16px;
	//	color: rgb(47, 152, 66);
	//}

	.cover {
		width: 180px;
		height: 180px;
		margin-left: 20px;

		img {
			width: 180px;
			height: 180px;
			border-radius: 50%;
			animation: roate 20s infinite linear; // 旋转专辑封面
			border:2px solid #808080b8;
		}
	}

	.volume-container {
		position: relative;
		left: 20px;
		top: -3px;
	}

	.volume-container .volume-wrapper {
		opacity: 0;
		transition: opacity .5s linear;
	}

	.volume-container:hover .volume-wrapper {
		opacity: 1;
	}

	.music-title {
	    font-size: 25px;
	    font-weight: 400;
	    color: rgb(3, 3, 3);
	    height: 6px;
	    line-height: 6px;
	}

	.music-artist {
		font-size: 15px;
	    font-weight: 400;
	    color: rgb(74, 74, 74);
    }

    .left-time {
    	font-size: 14px;
    	color: #999;
    	font-weight: 400;
    	width: 40px;
    }

    .icon {
    	cursor: pointer;
    }

    .ml20 {
    	margin-left: 20px;
    }

    .mt35 {
    	margin-top: 35px;
    }

    .volume-wrapper {
    	width: 60px;
    	display: inline-block;
    }
}

@keyframes roate {
	0% {
		transform: rotateZ(0)
	}
	100% {
		transform: rotateZ(360deg)
	}
}

```

然后在`src/components/Main.js`中添加音乐播放器组件 `Player` ，完整的代码请参照我发布到 Github 上的源代码。

## 最终效果

到此，基于 React 的音乐相册的全部功能已经完成了。最终的运行效果如下：

![][5]

源代码：[https://github.com/nnngu/MusicPhoto](https://github.com/nnngu/MusicPhoto)





  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518121965642.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518122145709.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518122335010.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518127202415.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/9/1518128437105.jpg