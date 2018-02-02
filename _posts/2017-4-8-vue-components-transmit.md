---
title: Vue 爬坑之路（二）—— 组件之间的数据传递
date: 2017-4-8
categories:
- Vue
tags:
- Vue
---

Vue 的组件作用域都是孤立的，不允许在子组件的模板内直接引用父组件的数据。必须使用特定的方法才能实现组件之间的数据传递。

首先用 vue-cli 创建一个项目，其中 App.vue 是父组件，components 文件夹下都是子组件。

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img6.png?Expires=1517562184&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=WVzqITwMBO2XVPQvb8nAnVEwvec%3D)




## 父组件向子组件传递数据

在 Vue 中，可以使用 props 向子组件传递数据。

### 子组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img7.png?Expires=1517562201&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=TK12xlg1gxw%2F3QFRXiJo4Aqbr1s%3D)

这是 header.vue 的 HTML 部分，logo 是在 data 中定义的变量。

如果需要从父组件获取 logo 的值，就需要使用 props: ['logo']

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img8.png?Expires=1517562212&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=Qhg2ZHgoRN0kg8WMzNfwBMEp4VA%3D)

在 props 中添加了元素之后，就不需要在 data 中再添加变量了

### 父组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img9.png?Expires=1517562225&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=hT09P5BhmuJc1%2FI7M2KIc%2FN%2FXf8%3D)

在调用组件的时候，使用 v-bind 将 logo 的值绑定为 App.vue 中定义的变量 logoMsg

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img10.png?Expires=1517562261&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=e40N%2Fx9r9Nu3PwycMJ038idcYs0%3D)

然后就能将App.vue中 logoMsg 的值传给 header.vue 了：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img11.png?Expires=1517562275&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=jgasDqZj8%2BufAxQPUJfbKz3jaKk%3D)


## 子组件向父组件传递数据

子组件主要通过事件传递数据给父组件

### 子组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img12.png?Expires=1517562291&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=rGt96wzmKvI8DSvkXKfNaNzpKvo%3D)

这是 login.vue 的 HTML 部分，当<input>的值发生变化的时候，将 username 传递给 App.vue

首先声明一个了方法 setUser，用 change 事件来调用 setUser

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img13.png?Expires=1517562368&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=8wdKKlh15e7r6u3eB6wfDGg6Q6g%3D)

在 setUser 中，使用了 $emit 来遍历 transferUser 事件，并返回 this.username

其中 transferUser 是一个自定义的事件，功能类似于一个中转，this.username 将通过这个事件传递给父组件

### 父组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img14.png?Expires=1517562378&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=LHUlbR8cxEvzduGi81rmfuNyCRk%3D)

在父组件 App.vue 中，声明了一个方法 getUser，用 transferUser 事件调用 getUser 方法，获取到从子组件传递过来的参数 username

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img15.png?Expires=1517562391&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=1iZi3XA5q8TgA4oYDr3LsojHjwo%3D)

getUser 方法中的参数 msg 就是从子组件传递过来的参数 username

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img16.gif?Expires=1517562402&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=JXl%2BC%2BWUq4ER5Sw2h1T9mWCV9Vc%3D)


## 子组件向子组件传递数据

Vue 没有直接子对子传参的方法，建议将需要传递数据的子组件，都合并为一个组件。如果一定需要子对子传参，可以先从传到父组件，再传到子组件。

为了便于开发，Vue 推出了一个状态管理工具 Vuex，可以很方便实现组件之间的参数传递

转载自：[Vue 爬坑之路（二）—— 组件之间的数据传递](http://www.cnblogs.com/wisewrong/p/6266038.html)