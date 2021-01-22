---
title: 问卷调查
tags: [问卷]
---
# 前言
最近pm问我能不能做类似于腾讯问卷星一样的问卷调查功能？就是pc端这边配置问卷，app用户填写，填写完保存可以查看自己的问卷信息。一开始本着自己的职业操守说：我先看看腾讯问卷星是怎么实现的，到时再给你答复。研究了一下感觉能做。就答应下来了。过几天设计稿下来了就开始动手做了。幸不辱命结果还是做出来了。话不多说，先看看效果吧。
### 1：pc问卷配置页
第一版我们的问卷配置页只有单选题，多选题和填空题。目前是左边点击一下题目类型，右边页面就会生成对应的体型。大概配置页面如下：
![](https://user-gold-cdn.xitu.io/2020/6/20/172d0cf5c177b33b?w=2250&h=1166&f=jpeg&s=146941)
简单说一下各种题型
* 填空题（其实页面和逻辑比较简单，我就一笔带过了）

![](https://user-gold-cdn.xitu.io/2020/6/20/172d0df9f400885b?w=1718&h=502&f=jpeg&s=41080)
* 单选题（单选题也就多了个选项说明和分数，以及操作按钮换位置或者删除当前选项）

![](https://user-gold-cdn.xitu.io/2020/6/20/172d0e24389df63c?w=1750&h=876&f=jpeg&s=90039)
* 多选题 （基本跟单选一样 不过用户可以选择多个选项）

![](https://user-gold-cdn.xitu.io/2020/6/20/172d0e835e8bac65?w=1774&h=1010&f=jpeg&s=111922)
### 2：pc问卷配置页特殊跳转问题

![](https://user-gold-cdn.xitu.io/2020/6/20/172d0ea266afae46?w=1814&h=676&f=jpeg&s=68661)
目前我们支持3种跳转方式来满足不同用户的需求，跳转指定页和按选项跳转页面以及定制方案（为特殊用户提供定制方案从而推销自己的产品，当然这种是要另外收钱的）

目前问卷调查配置页已完成，用户可以根据自己的要求来配置问卷。配置好问卷之后，用户可以在app来作答完成问卷。
### 3:app问卷
pc配置页出来之后 app填写，大概页面如下。

![](https://user-gold-cdn.xitu.io/2020/6/20/172d0f588659ba20?w=828&h=1792&f=jpeg&s=80474)
### 4:遇到的问题
在做没有特殊跳转的问卷时候，写的一帆风顺，但做特殊页面调整的时候，当时卡住了，`比如说用户从第三页跳到第六页，我怎么让Ta从第六页回到第三页而不是第五页`，因为我这一开始是根据下标去判断是问卷的第几页的。当时纠结了一下，后来用一个数组记录了下标，通过`上一步下一步`去维护这个像栈一样的数组。
上一步下一步的相关代码：
```
        if(step === 1) {
          for (let question of this.currentPage && this.currentPage.questionList) {
            let flag = false
            let isRequired = false
            if (question.optionList) {
              for (let optionItem of question.optionList) {
                if (optionItem.checked) {
                  flag = true
                }
                if (optionItem.ifRequired) {
                  isRequired = true
                }
              }
            }
            if (!question.optionId && !flag && isRequired) {
              Toast.start({
                info: '请填完所有的选项',
                type: 'error',
                time: 1500
              })
              return false
            }
          }
          if (this.currentSteps === this.MaxPageCount || this.currentPage.pageRedirectType === 'CUSTOM_CONTENT') {
            this.save()
            return false
          }
          if (this.currentPage.pageRedirectType === 'CUSTOM_OPTION') {
            for (let [index, pageItem] of this.questionPageList.entries()) {
              if (this.pageId === pageItem.pageId) {
                this.currentSteps = index
                return false
              }
            }
          }
          if(this.currentPage.pageRedirectType === 'CUSTOM_PAGE') {
            for (let [index, pageItem] of this.questionPageList.entries()) {
              if (this.currentPage.pageRedirectPageId === pageItem.pageId) {
                this.currentSteps = index
                return false
              }
            }
          }
          this.currentSteps = this.currentSteps + 1
        } else {
          this.stepsArr = this.stepsArr.slice(0, -1)
          this.currentSteps = this.stepsArr.pop()
        }
```
### 总结
这个问卷需求做下来，其实有一些自己没有考虑好。当初技术讨论的时候，我们leader问我问卷是一页一页提交还是一起提交，当时脑子瓦特了没考虑那么多，就选了一起提交。毕业也有一年了，希望自己不要盲目自信，多总结经验，多沉淀自己的技术。多向大佬学习。另外码字不容易，希望大家点个赞。前端路漫漫，与君共勉之。
