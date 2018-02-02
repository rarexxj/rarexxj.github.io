---
title: Vue 爬坑之路（七）—— 监听滚动事件 实现动态锚点

date: 2017-9-10
categories:
- Vue
tags:
- Vue
---

前几天做项目的时候，需要实现一个动态锚点的效果
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img50.gif?Expires=1517564549&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=lV8vrr8iwoT1jER000%2BCLXedKiE%3D)


如果是传统项目，这个效果就非常简单。但是放到 Vue 中，就有两大难题：

1. 在没有 jQuery 的 animate() 方法的情况下，如何实现平滑滚动？

2. 如何监听页面滚动事件？

在浏览了大量文章、进行多次尝试之后，终于解决了这些问题

期间主要涉及到了 setTimeout 的递归用法，和 Vue 生命周期中的 mounted



## 锚点实现

在实现平滑滚动之前，得先确保基本的锚点功能

如果没有其他要求，直接用'<a href="#id">'是最简单粗暴的办法

但是为了满足后续的要求，必须另外想办法



首先在父组件（表单）中添加 class="d_jump" 作为钩子
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img51.png?Expires=1517564566&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=U0R%2BEMCVQ7G3HSVAn%2Ba88x6pNvY%3D)


然后在子组件中添加一个 jump 方法
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img52.png?Expires=1517564577&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=wRYMhqZRRWQcOwCW1CiOs86VpH8%3D)


```
jump (index) {
    let jump = document.querySelectorAll('.d_jump')
    // 获取需要滚动的距离
    let total = jump[index].offsetTop
    // Chrome
    document.body.scrollTop = total
    // Firefox
    document.documentElement.scrollTop = total
    // Safari
    window.pageYOffset = total
},
```
通过 offsetTop 获取对象到窗体顶部的距离，然后赋值给 scrollTop，就能实现锚点的功能

需要注意的是，各浏览器下获取 scrollTop 有所差异

Chrome： document.body.scrollTop

Firefox： document.documentElement.scrollTop



## 平滑滚动

仅仅是锚点是不够的，这样的跳转十分突兀

为了更好的用户体验 ，需要将滚动的过程展现出来

如果有 jQuery 实现平滑滚动就非常简单：

```$('html body').animate({scrollTop: total}, 500);```

可惜没如果~~

在看了好些文章之后，有了一个大概的开发思路：

将总距离分成很多小段，然后每隔一段时间跳一段

只要每段的时间足够短，频次足够多，在视觉上就是正常的平滑滚动了

```
// 平滑滚动，时长500ms，每10ms一跳，共50跳
// 获取当前滚动条与窗体顶部的距离
let distance = document.documentElement.scrollTop || document.body.scrollTop
// 计算每一小段的距离
let step = total / 50
(function smoothDown () {
  if (distance < total) {
    distance += step
　　// 移动一小段
    document.body.scrollTop = distance
    document.documentElement.scrollTop = distance
　　// 设定每一次跳动的时间间隔为10ms
    setTimeout(smoothDown, 10)
  } else {
　　// 限制滚动停止时的距离
    document.body.scrollTop = total
    document.documentElement.scrollTop = total
  }
})()
```


实际情况下，得考虑向上滚动和向下滚动两种情况，完整的代码为：

```
    jump (index) {
        // 用 class="d_jump" 添加锚点
        let jump = document.querySelectorAll('.d_jump')
        let total = jump[index].offsetTop
        let distance = document.documentElement.scrollTop || document.body.scrollTop
        // 平滑滚动，时长500ms，每10ms一跳，共50跳
        let step = total / 50
        if (total > distance) {
          smoothDown()
        } else {
          let newTotal = distance - total
          step = newTotal / 50
          smoothUp()
        }
        function smoothDown () {
          if (distance < total) {
            distance += step
　　　　　　　document.body.scrollTop = distance
            document.documentElement.scrollTop = distance
            setTimeout(smoothDown, 10)
          } else {
            document.body.scrollTop = total
            document.documentElement.scrollTop = total
          }
        }
        function smoothUp () {
          if (distance > total) {
            distance -= step
　　　　　　　document.body.scrollTop = distance
            document.documentElement.scrollTop = distance
            setTimeout(smoothUp, 10)
          } else {
            document.body.scrollTop = total
            document.documentElement.scrollTop = total
          }
       }
     }
```




## 修改锚点状态

在上面展示的效果中，当页面滚动的时候，锚点的激活状态会有相应的改变

其实这个效果并不难，只需要监听页面滚动事件，然后根据滚动条的距离修改锚点状态就可以了

但是在 Vue 中，应该在什么地方监听滚动事件呢？

```
  mounted: function () {
    this.$nextTick(function () {
      window.addEventListener('scroll', this.onScroll)
    })
  },
  methods: {
    onScroll () {
      let scrolled = document.documentElement.scrollTop || document.body.scrollTop
　　　 // 586、1063分别为第二个和第三个锚点对应的距离
      if (scrolled >= 1063) {
        this.steps.active = 2
      } else if (scrolled < 1063 && scrolled >= 586) {
        this.steps.active = 1
      } else {
        this.steps.active = 0
      }
    }
  }
```
上面的代码中，我先写了一个修改锚点状态的方法 onScroll，然后在 mounted 中监听 scroll 事件，并执行 onScroll 方法

mounted 是 Vue 生命周期中的一个状态，在这个状态下，$el （vue 实例的根元素）已经创建完毕，但还没有加载数据

从结果上看，也可以在 created 状态下监听 scroll 事件

如果对 mounted 和 created 还不够了解，可以参考[官方文档·生命周期图示](https://cn.vuejs.org/v2/guide/instance.html#%E5%AE%9E%E4%BE%8B%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)



## 后记

上面只能算是一个应急之法，而且这种操作 DOM 的方法，并不符合 Vue 的设计理念

待我研究出更合理更高效的办法之后，再发出来分享~

转载自：[Vue 爬坑之路（七）—— 监听滚动事件 实现动态锚点](https://www.cnblogs.com/wisewrong/p/6495726.html)