---
title: vue音乐
tags: [Vue, 网易云]
---

![](https://user-gold-cdn.xitu.io/2018/7/29/164e521423426451?w=1024&h=666&f=jpeg&s=213382)
# 前言
最近在自学vue,打算自己仿一个项目来实战一下，由于本人很喜欢听歌，所以就选择了网易云音乐，在这与大家分享一下自己所遇到的问题，其中也有些不足之处也希望大家提一些宝贵的意见，互相学习，一起进步。
## 关于项目使用的技术栈
* Vue：采用Vue的语法

* Vuex：实现不同组件之间的状态共享

* vue-router：单页应用路由管理必备

* axios：发起http请求

* SASS(SCSS)：css预处理语言
## 项目
由于时间有限，只是做了个页面的播放功能，其中用到了网易云音乐的API[网易云](https://binaryify.github.io/NeteaseCloudMusicApi/#/?id=%E6%8E%A5%E5%8F%A3%E6%96%87%E6%A1%A3)，有兴趣的可以去玩玩，其中也涉及到了一些知识点，在这与大家分享一下。
## 上图
整个效果：
![](https://user-gold-cdn.xitu.io/2018/7/26/164d6ff2ccc6e2f3?w=294&h=510&f=gif&s=708592))
## 分享做这个单页面的过程
这就是一个header组件，一个footer组件，一个musicList组件和一个paly组件组成的单页面。
### 1. 如何获取音乐的数据
我这是从网易云音乐api扒出来的，扒出来之后新建一个文件，把数据放进去，之后通过axios获取，部分代码如下：

```
actions: {
    getData({ commit,state }) {
      if (localStorage.musics !== '[]' && localStorage.musics) {
        state.musicData = JSON.parse(localStorage.musics);
        return;
      }
      return new Promise((resolve, reject) => {
        Vue.axios.get('music-data')
            .then (res => {
              if (res.data.error === 0) {
                state.musicData = res.data.musicData;
                localStorage.musics = JSON.stringify(state.musicData);
              }
            })
            .then(() => {
              commit('toggleMusic',0)
            });
        resolve();
      });
    }
  }
```
### 2. 删除功能
我是在这删除这个图标下绑定了一个事件，主要就二句代码:

```
<span v-on:click="del(index)" class="del-icon"></span>
在methods定义del事件就好了
        del(index){
            this.$store.commit('del',index);
        }
```
### 3. 尾部的播放控制
尾部的播放功能我一开始遇到了一个难题就是如何获取歌曲的时间和控制播放的进度。后来通过查找资料和百度解决了

**获取歌曲时间的部分代码如下：**

```
 <span class="start">{{transformTime(now)}}</span>
 js部分代码
  this.nativeAudio = document.querySelector('audio');
    this.nativeAudio.addEventListener('play', () => {
      this.totalTime = this.transformTime(this.nativeAudio.duration);
      this.now = this.nativeAudio.currentTime;
      setInterval(() => {
        this.now = this.nativeAudio.currentTime;
      }, 1000)
    })
    
    transformTime(seconds) {
      let m, s;
      m = Math.floor(seconds / 60);
      m = m.toString().length == 1 ? ('0' + m) : m;
      s = Math.floor(seconds - 60 * m);
      s = s.toString().length == 1 ? ('0' + s) : s;
      return m + ':' + s;
    }
```
**控制播放进度的部分代码如下**

```
changeTime(event) {
      let progressBar = this.$refs.progressBar;
      let coordStart = progressBar.getBoundingClientRect().left;  //getBoundingClientRect()方法返回元素的大小及其相对于视口的位置
      let coordEnd = event.pageX;
      this.nativeAudio.currentTime = (coordEnd - coordStart) / progressBar.offsetWidth * this.nativeAudio.duration;
      this.now = this.nativeAudio.currentTime;
      this.nativeAudio.play();
      this.$store.commit('play', true);
    },
touchMove(event) {
      let progressBar = this.$refs.progressBar;
      let coordStart = progressBar.getBoundingClientRect().left;
      let coordEnd = event.touches[0].pageX;
      this.$refs.now.style.width = ((coordEnd - coordStart) / progressBar.offsetWidth).toFixed(3) * 100 + '%';  //toFixed(3)保留小数点后3位
    },
touchEnd(event) {
      this.nativeAudio.currentTime = this.$refs.now.style.width.replace('%', '')/100 * this.nativeAudio.duration;
      this.now = this.nativeAudio.currentTime;
      this.nativeAudio.play();
      this.$store.commit('play', true);
    },
```
### 4. 换肤
换肤主要提供了四种颜色，红色 蓝色 黑色 和绿色，样式使用的是flex布局，主要css代码如下：

```
.skin {
        position: absolute;
        display: flex;
        flex-direction: column;
        bottom: 50px;
        right: 15px;
        width: 30px;
        .skin-colors {
          flex: 4;
          width: 100%;
          display: flex;
          justify-content: center;
          align-items: center;
          flex-direction: column;
          .selected {
            border: 1px solid white;
          }
          i {
            flex: 1;
            display: inline-block;
            width: 20px;
            height: 20px;
            cursor: pointer;
            border-radius: 10px;
            margin-bottom: 5px;
          }
          i.one {
            background-color: #B72712;
          }
          i.two {
            background-color: #1565C0;
          }
          i.three {
            background-color: #212121;
          }
          i.four {
            background-color: #1B5E20;
          }
        }
        .icon-skin {
          flex: 1;
          width: 100%;
          height: 30px;
          background-repeat: no-repeat;
          background-size: contain;
          margin-top: 3px;
          cursor: pointer;
        }
        .icon-skin-red {
          background-image: url('./skinRed.svg');
        }
        .icon-skin-green {
          background-image: url('./skinGreen.svg');
        }
        .icon-skin-blue {
          background-image: url('./skinBlue.svg');
        }
        .icon-skin-black {
          background-image: url('./skinBlack.svg');
        }
```
### 5. 控制歌曲的上一首下一首的播放
部分代码如下：

```
    prev() {
      this.audio.index = this.audio.index === 0 ? this.musicData.length - 1 : (--this.audio.index);
      this.$store.commit('toggleMusic', this.audio.index);
    }
    
    next() {
      this.audio.index = this.audio.index === this.musicData.length - 1 ? 0 : (++this.audio.index);
      this.$store.commit('toggleMusic', this.audio.index);
    }
```
**总结：通过模仿这个项目更加清楚地了解各组件之前的使用和不同组件的状态共享。当然也遇到了一些坑，文章写到这里，也没有完全写完，只写了一个单页面，但也算是一个小小的总结，接下来附上我的源码：[项目源码](https://github.com/duzuimoye/vue-music-tool)，有兴趣的朋友可以看看顺便帮忙点个star和fork,也希望能帮助到一些朋友。作为一名快要成为大四的学生，时间真的宝贵，对待学习也不敢懈怠，如果大家有什么好的想法的话可以联系我的qq:137032979.码字不容易，希望大家点个赞。前端路漫漫，与君共勉之。**

![](https://user-gold-cdn.xitu.io/2018/7/29/164e52214f922131?w=424&h=452&f=jpeg&s=13847)