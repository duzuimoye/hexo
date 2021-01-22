---
title: 微信扫码登录
tags: 微信扫码
---
# 前言
最近leader给了我一个需求，让我微信扫码登录我们的pc系统。一开始拿到这个需求，看了二三次微信官方文档之后，本以为会很简单，半天之内就能搞定。直接奥利给，撸代码。结果```啪啪啪打脸，把所有的坑都踩过了。```不过幸不辱命，还是把这个需求写完了。
![image](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3879161035,1816984358&fm=26&gp=0.jpg)
### 1:准备工作
网站应用微信登录是基于OAuth2.0协议标准构建的微信OAuth2.0授权登录系统。 在进行微信OAuth2.0授权登录接入之前，在微信开放平台注册开发者帐号，并拥有一个已审核通过的网站应用，并获得相应的```AppID和AppSecret```，申请微信登录且通过审核后，可开始接入流程。审核大概要一周时间外加300rmb。[微信官方文档](https://developers.weixin.qq.com/doc/oplatform/Website_App/WeChat_Login/Wechat_Login.html)
### 2:整理思路
审核通过之后整理整个需求思路。大概画了这样一个流程图。![image2020](https://segmentfault.com/img/bVbFdTj)
### 3:所踩的坑

1.外链链接报错的问题

由于我们当时没没定是内嵌还是外链二维码，所以当时第一种尝试方案就是外链二维码扫码登录。当时是请求后端接口拿到这个二维码地址。点击这个地址发现```Scope 参数错误或没有 Scope 权限```结果查看文档发现后端返回的```redirect_uri```没有进行UrlEncode处理。前后端一定得**多看**几遍文档!!!!!
当时拿到二维码链接的时候，一直想要通过```img```标签把二维码显示出来，试了不行，用```iframe```试了行是可行。但是样式不对。然后二维码样式直接覆盖也没有用。之后又试了```qrcode插件```，二维码样式是对了但是扫码之后结果却不是这样的。哎。一言难尽啊！
2.内嵌二维码样式显示
根据官方文档引入JS文件
> <script type="text/javascript" src="https://res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js"></script>
>  其实这个js也是通过iframe来加载二维码的，之后微信登录的时候引入对象。
> 
        var obj = new WxLogin({
          id: 'login_container', // 需要显示的容器id
          appid: '************', // appid 
          scope: 'snsapi_login', // 网页默认即可
          redirect_uri: redirectUrl, // 授权成功后回调的url
          state: dayjs().unix(),// 用来校验
          style: 'black', // 二维码的样式
          href: 'https://******/qrcode.css' // 外部css文件url，需要https
        })

**注意这个redirect_uri一定要写对，不然二维码显示不出来**，如果发现二维码过大或者你不想要的样式，你可以在href填入一个链接。用其中的样式覆盖。
> 
    .loginPanel normalPanel {display: flex;}
    .impowerBox .qrcode {width: 200px;}
    .impowerBox .title {display: none;}
    .impowerBox .info {width: 200px;}
    .status_icon {display: none}
    .impowerBox .status {text-align: center;}
    
### 最终效果：
![image](https://segmentfault.com/img/bVbFd54)
大功告成，虽然结果出来了，但是过程却是一波三折。
### 总结
本人19年毕业的，从事前端开发也有9个月了，加上实习的话都一年多了。
希望自己不要盲目自信，多总结经验，多沉淀自己的技术。多向大佬学习。另外码字不容易，希望大家点个赞。前端路漫漫，与君共勉之。

