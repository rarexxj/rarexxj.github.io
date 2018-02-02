---
title: Vue 爬坑之路（九）—— 用正确的姿势封装组件

date: 2017-9-21
categories:
- Vue
tags:
- Vue
---

迄今为止做的最大的 Vue 项目终于提交测试，天天加班的日子终于告一段落。。。

在开发过程中，结合 Vue 组件化的特性，开发通用组件是很基础且重要的工作

通用组件必须具备高性能、低耦合的特性

为了满足这些特性，开发的时候有很多需要注意的地方，这里我和大家分享一下我的心得



## 数据从父组件传入

为了解耦，子组件本身就不能生成数据。即使生成了，也只能在组件内部运作，不能传递出去。

父对子传参，就需要用到 props，通常的 props 是这样的：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img55.png?Expires=1517565806&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=vcYAaU6fd9i%2BXDWHPvmOdXIyGfE%3D)


但是通用组件的的应用场景比较复杂，对 props 传递的参数应该添加一些验证规则
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img56.png?Expires=1517565816&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=7IAtxY2R5rsIutSt%2FAGx4FgyldQ%3D)



对于通过 props 传入的参数，不建议对其进行操作，因为会同时修改父组件里面的数据

// vue2.5已经针对 props 做出优化，这个问题已经不存在了

如果一定需要有这样的操作，可以这么写：

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img57.png?Expires=1517565831&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=wg1W3naObSHs3t%2FNfXIAsSKlzR8%3D)


为什么不直接写 let myData = this.data 呢？

因为直接赋值，并不能解除双向绑定。改变 myData 的时候，会改变 this.data，父组件的 data 也会变

而通过 JSON 转换之后，就能任性的操作了


## 在父组件处理事件

在通用组件中，通常会需要有各种事件，

比如复选框的 change 事件，或者组件中某个按钮的 click 事件

这些事件的处理方法应当尽量放到父组件中，通用组件本身只作为一个中转
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img58.png?Expires=1517565841&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=2i7FBff8mjzMUFz%2Fl1h%2FLMPox9E%3D)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img59.png?Expires=1517565855&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=%2F9cSbgtSlOGPky5lD8n61WZhhtk%3D)


这样既降低了耦合性，也保证了通用组件中的数据不被污染

关于 $emit 的具体写法，可以参考[《 Vue 爬坑之路（二）—— 组件之间的数据传递》](http://www.xiexijie.top/vue/2017/04/08/vue-components-transmit/#)

不过，并不是所有的事件都放到父组件处理

比如组件内部的一些交互行为，或者处理的数据只在组件内部传递，这时候就不需要用 $emit 了



## 记得留一个 slot

一个通用组件，往往不能够完美的适应所有应用场景

所以在封装组件的时候，只需要完成组件 80% 的功能，剩下的 20% 让父组件通过 solt 解决

关于 slot 的具体用法，可以参考我的博客 [《 Vue 爬坑之路（五）—— 组件进阶 》](http://www.xiexijie.top/vue/2017/05/10/vue-components-advanced/#)


上面是一个通用组件，在某些场景中，右侧的按钮是 “处理” 和 “委托”。在另外的场景中，按钮需要换成 “查看” 或者 “删除”

在封装组件的时候，就不用写按钮，只需要在合适的位置留一个 slot，将按钮的位置留出来，然后在父组件写入按钮
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img60.png?Expires=1517565877&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=8M%2Bm%2Bpt0rSNHcaagsrDqefHzUJw%3D)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img61.png?Expires=1517565884&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=2RsbSiWLqUZ8CeswoQY4esMy1Ns%3D)


开发通用组件的时候，只要不是独立性很高的组件，建议都留一个 slot，即使还没想好用来干什么


## 不要依赖 Vuex

【《 Vue 爬坑之路（四）—— 与 Vuex 的第一次接触》](http://www.xiexijie.top/vue/2017/04/26/vuex/#)

父子组件之间是通过 props 和 自定义事件 来传参，非父子组件通常会采用 Vuex 传参

但是 Vuex 的设计初衷是用来管理组件状态，虽然可以用来传参，但并不推荐

因为 Vuex 类似于一个全局变量，会一直占用内存

在写入数据庞大的 state 的时候，就会产生内存泄露



当页面刷新的时候，Vuex 会初始化，同时也会丢失编辑过的数据

如果刷新页面时需要保留数据，可以通过 url 或者 sessionstorage 保存 id，然后 ajax 请求数据



## 合理运用 scoped 编写 CSS

在编写组件的时候，可以在 `<style>` 标签中添加 scoped，让标签中的样式只对当前组件生效

但是一味的使用 scoped，肯定会产生大量的重复代码

所以在开发的时候，应该避免在组件中写样式

当全局样式写好之后，再针对每个组件，通过 scoped 属性添加组件样式

转载自：[Vue 爬坑之路（九）—— 用正确的姿势封装组件](https://www.cnblogs.com/wisewrong/p/6834270.html)