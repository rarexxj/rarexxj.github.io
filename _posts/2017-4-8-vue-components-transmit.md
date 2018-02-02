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

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img6.png?Expires=1517555117&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=7YmfliRbma3cl%2FsxonniQ9BTGKo%3D)




## 父组件向子组件传递数据

在 Vue 中，可以使用 props 向子组件传递数据。

### 子组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img7.png?Expires=1517555132&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=LMu7BBfMAVLf6bzGmOEeb%2FAdLYY%3D)

这是 header.vue 的 HTML 部分，logo 是在 data 中定义的变量。

如果需要从父组件获取 logo 的值，就需要使用 props: ['logo']

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img8.png?Expires=1517555146&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=RcbLhUGiyL3gyQzdVEj3p8UwoaM%3D)

在 props 中添加了元素之后，就不需要在 data 中再添加变量了

### 父组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img9.png?Expires=1517555160&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=Y1qvWLesLaNATpDYcuhpQ6kdi%2Fc%3D)

在调用组件的时候，使用 v-bind 将 logo 的值绑定为 App.vue 中定义的变量 logoMsg

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img10.png?Expires=1517555173&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=l1NKKzyf4A%2BYcekuLetmlhg6AXQ%3D)

然后就能将App.vue中 logoMsg 的值传给 header.vue 了：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img11.png?Expires=1517555184&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=v%2BUYgb2umB%2FJIqCe7x8%2BV34R4ms%3D)


## 子组件向父组件传递数据

子组件主要通过事件传递数据给父组件

### 子组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img12.png?Expires=1517555198&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=DhWshHBBMgir09%2Bh7GQAJlIQzVM%3D)

这是 login.vue 的 HTML 部分，当<input>的值发生变化的时候，将 username 传递给 App.vue

首先声明一个了方法 setUser，用 change 事件来调用 setUser

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img13.png?Expires=1517555209&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=stApb%2Fxev%2FMfb2%2FpCCDJ0nFJvlI%3D)

在 setUser 中，使用了 $emit 来遍历 transferUser 事件，并返回 this.username

其中 transferUser 是一个自定义的事件，功能类似于一个中转，this.username 将通过这个事件传递给父组件

### 父组件部分：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img14.png?Expires=1517555220&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=grJNnmIYxjuBUQoX5cfFnjREDmg%3D)

在父组件 App.vue 中，声明了一个方法 getUser，用 transferUser 事件调用 getUser 方法，获取到从子组件传递过来的参数 username

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img15.png?Expires=1517555233&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=%2FOyiYuntcgdLZ%2Bxr6l1SXduGN0E%3D)

getUser 方法中的参数 msg 就是从子组件传递过来的参数 username

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/img16.gif?Expires=1517555243&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=PuPznVZygk0irZ7U%2FNlHvbfXXmc%3D)


## 子组件向子组件传递数据

Vue 没有直接子对子传参的方法，建议将需要传递数据的子组件，都合并为一个组件。如果一定需要子对子传参，可以先从传到父组件，再传到子组件。

为了便于开发，Vue 推出了一个状态管理工具 Vuex，可以很方便实现组件之间的参数传递

转载自：[Vue 爬坑之路（二）—— 组件之间的数据传递](http://www.cnblogs.com/wisewrong/p/6266038.html)