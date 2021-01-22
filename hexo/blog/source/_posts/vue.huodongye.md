---
title: vue活动页
tags: [Vue, H5]
---
# 前言
最近，公司的PM提了一个需求 自动获取七天新上传的并且审核通过的商品做成固定的链接的一个活动页面。当时想了一想就用vue做了，感觉效果还行，在这分享一下我是如何做的 希望对大家有一点点帮助。
## 效果图
[效果](https://res.zudeapp.com/activity/20190227/index.html)



## 1：页面的构建及优化
所谓的活动页 首先第一步肯定是把页面切出来，这里就是2*n页面 我这里用的就是**grid**布局（也可以用flex）我主要讲三个点：

### 1:关于图片的优化

  由于后段传过来的图片大小不一样，我就对图片做了做了一下优化 。**整个图片在填充盒子的同时保留其长宽比**
代码：

```
 .product-img img {
  object-fit: contain;
  width: auto;
  height: auto;
  max-width: 100%;
  max-height: 100%;
  margin: 0 auto;
}
```
为了不让图片觉得突兀 我们可以给图片的盒子设置一个伪元素 

```
.product-img::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  -webkit-transform: translate3d(0, 0, 0);
  transform: translate3d(0, 0, 0);
  z-index: 1000;
  width: 100%;
  height: 100%;
  border-radius: .1rem;
  background: rgba(85, 85, 85, 0.05);
}
```
### 2:关于页面数据还没加载出来的优化
由于从后台获取数据需要一定的时间 当数据没加载进来的时候会出现问题（也许就是一秒但这也会给用户带来不好的体验感。）

```
<div class="container" :class="productList.length ? 'show': ''">
```
当数据没加载的时候我就设置**opacity为0**，当数据出来的时候就设置**opacity：1**
### 3:关于价格的优化
由于设计稿的需求是价格的整数的字体要比小数要大，所以就把整数和小数分别用spilt分隔来了。然后在给整数的字体比小数点的字体大一号就行了。

```
<div class="product-price">￥<span class="em">{{String(product.price).split('.')[0]}}</span>.{{String(product.price).split('.')[1]||'0'}}/天</div>
```
# 2：获取数据
从后台获取数据是很重要的一部分 由于后段给了二个参数 一个是当前页 一个是一个页面有多少条数据。

```
      getList(cb){
        this.getActivityInfoById(this.curPage,this.pageSize).then((data = {})=>{
          this.total = data.total;
          if(( this.curPage * this.pageSize) >= this.total && document.readyState == "complete") {
            this.isMaxPage = true;
          }
          this.productList = this.productList.concat(data.rows || []);
          cb && cb(data)
        })
      }
```

```
      getActivityInfoById: function(start, length) {
        return axios
          .get(this.api.getActivityInfoById, {
            params: {
              start: start,
              length: length
            }
          })
          .then(function(res) {
            return res.data.data;
          });
      },
```
# 3:分页
所谓的活动页肯定要做分页处理

```
onPage(){
        const scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop; 
        const bodyHeight = document.body.offsetHeight;
        const clientHeight = window.innerHeight;
        if(scrollTop + clientHeight < bodyHeight){
          return;
        }
        if(this.isGetList) return;
        if(this.total < this.curPage * this.pageSize){
          return;
        }
        
        this.curPage++;
        this.isGetList = true;
        this.getList(()=>{
          this.isGetList = false;
        });
      },
```
# 4:下拉刷新的优化
**当数据还在加载中显示loading，当数据加载完成是显示扯到底了**
```
<div class="footer" v-if="isMaxPage">- 不要扯了 已经扯到底了 -</div>
<div class="footer" v-if="!isMaxPage">- loading -</div>
```
# 5：懒加载
由于这个活动页图片有点多 所以用了懒加载

```
      lazyLoad: function() {
        var seeHeight = document.documentElement.clientHeight; // 可见区域高度
        var imgs = document.getElementsByTagName('img');
        for (var i = this.lazyLoadIndex; i < imgs.length; i++) {
          if (
            imgs[i].getBoundingClientRect().top < seeHeight &&
            imgs[i].dataset.src &&
            imgs[i].getAttribute('src') !== imgs[i].dataset.src
          ) {
            imgs[i].setAttribute('src', imgs[i].dataset.src);
            this.lazyLoadIndex++;
          }
        }
      },
```

# 总结
作为一个毕业的大四学生，这是我来公司实习做的活动页，希望可以帮助大家，互相学习，一起进步。当然也有一些不足之处，请大家多多指教。码字不容易，希望大家点个赞。前端路漫漫，与君共勉之。