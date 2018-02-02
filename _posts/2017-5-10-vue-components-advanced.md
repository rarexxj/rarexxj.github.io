---
title: Vue 爬坑之路（五）—— 组件进阶
date: 2017-5-10
categories:
- Vue
tags:
- Vue
---

组件（Component）是 Vue.js 最强大的功能之一，之前的文章都只是用到了基本的封装功能，这次将介绍一些更强大的扩展。



一、基本用法

在使用 vue-cli 创建的项目中，组件的创建非常方便，只需要新建一个 .vue 文件，然后在 <template> 中写好 HTML 代码，一个简单的组件就完成了
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img33.png?Expires=1517562987&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=JUvIFnc%2F3KBBPU5xiIVDnZj3clA%3D)


一个完整的组件，除了 <template> 以外，还有 <script> 和 <style>

需要注意的是，<script> 中的 data 必须是函数
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img34.png?Expires=1517563108&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=m4SBN6XT%2FOGsX34Gqx5oL0wNVSM%3D)




然后在其他文件的 js 里面引入并注册，就能直接使用这个组件了
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img35.png?Expires=1517563118&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=XTT%2FC3BNsFCcG9kkuWig9ier%2Bqg%3D)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img36.png?Expires=1517563125&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=gM6qY5dAbCTovwc%2B4pgbdBnfUj4%3D)







二、使用 slot 分发内容

开发过程中，常常需要在子组件内添加新的内容，这时候可以在子组件内部留一个或者多个插口 <slot>
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img37.png?Expires=1517563133&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=4KEwZpH3XJebqYb%2F0IGHqvpetfQ%3D)




然后在调用这个子组件的时候加入内容
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img38.png?Expires=1517563141&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=qSZi9CzUFP1msKeT18zgcGBMl5Y%3D)


添加的内容就会分发到对应的 <slot> 中



<slot> 中还可以作为一个作用域，在子组件中定义变量，然后在父组件中自定义渲染的方式
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img39.png?Expires=1517563150&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=JzyRpKz2AnuAnn1QbITv1wFfig4%3D)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img40.png?Expires=1517563162&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=AJoz2r6w0RGAftAJbL7eJN9IWLw%3D)



这个示例中，首先在子组件中添加 <slot>，并在子组件中定义了数组变量 navs

然后在父组件中以作用域 <template> 添加内容，其中 scope 是固有属性，它的值对应一个临时变量 props

而 props 将接收从父组件传递给子组件的参数 navs
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img41.png?Expires=1517563170&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=6dvC8TD3kRa9YYNOSA%2BcpfLT2CY%3D)






三、动态组件

Vue 还可以将多个子组件，都挂载在同一个位置，通过变量来切换组件，实现 tab 菜单这样的效果
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img42.gif?Expires=1517563178&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=4kqtn19fLbmd5gdnIMy2h4N%2FLH8%3D)


这样的功能可以通过路由 vue-router 实现，但路由更适合较大的组件，而且 url 会有相应的改变

Vue 自身保留的 <component> 元素，可以将组件动态绑定到 is 特性上，从而很方便的实现动态组件切换
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img43.png?Expires=1517563187&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=DX5yrewtzkqj4IKoH9ZkCuIOXhg%3D)


上例中，当 tabView 的值改变，<component> 就会渲染对应的组件，和路由的效果十分类似，但是地址栏并没有发生改变

但这样一来，每次切换组件都会重新渲染，无法保留组件上的数据。这时可以使用 keep-alive 将组件保留在内存中，避免重新渲染
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img44.png?Expires=1517563208&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=BXo5I5Qyd954cUMwiBvm3yaYSVM%3D)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img45.gif?Expires=1517563215&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=ACmcSCfgxpE%2BEPwNL6DyI7%2Fdn5U%3D)









四、递归组件

当组件拥有 name 属性的时候，就可以在它的模板内递归的调用自己，这在开发树形组件的时候十分有效
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img46.png?Expires=1517563228&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=%2FzYxsKYJXz8oBudyVGqUTGqtISU%3D)


上面是一个子组件，定义了 name 为 simple03，然后在模板中调用自身，结合 v-for 实现递归

为了防止出现死循环，在调用自身的时候，加入了 v-if 作为判定条件

父组件中调用的时候，需要通过 props 传入一个 tree：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img47.png?Expires=1517563239&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=fj7A5JM%2BpIgWH1PkZUo%2F%2BDfnNKo%3D)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img48.png?Expires=1517563246&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=cZHumt91Z5nUncARBVg9CENvhyg%3D)






最终渲染结果：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img49.png?Expires=1517563256&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=y4TGT7kRQmhs0Lk6qrFQztk93V8%3D)


转载自：[Vue 爬坑之路（五）—— 组件进阶](https://www.cnblogs.com/wisewrong/p/6380903.html)