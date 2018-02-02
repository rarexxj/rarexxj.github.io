---
title: Vue 爬坑之路（三）—— 使用 vue-router 跳转页面
date: 2017-4-23
categories:
- Vue
tags:
- Vue
---

使用 Vue.js 做项目的时候，一个页面是由多个组件构成的，所以在跳转页面的时候，并不适合用传统的 href，于是 vue-router 应运而生。

官方文档： [https://router.vuejs.org/zh-cn/essentials/getting-started.html](https://router.vuejs.org/zh-cn/essentials/getting-started.html)



有很多朋友找我要 demo，但是博客中的这个案例被我删掉了，我只好随手写了一个超简单的 demo，希望能有所帮助

链接：[http://pan.baidu.com/s/1nvqfA1V](http://pan.baidu.com/s/1nvqfA1V) 密码：79bv



这次的实例主要实现下图的效果：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img17.png)

项目结构：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img18.png)


## 配置 Router

 用 vue-cli 创建的初始模板里面，并没有 vue-router，需要通过 npm 安装
```
cnpm i vue-router -D
```
安装完成后，在 src 文件夹下，创建一个 routers.js 文件，和 main.js 平级

然后在 router.js 中引入所需的组件，创建 routers 对象

```
import Home from './components/home.vue'

const routers = [
  {
    path: '/home',
    name: 'home',
    component: Home
  }，
  {
    path: '/',
    component: Home
  }，
]
export default routers

```
在创建的 routers 对象中， path 配置了路由的路径，component 配置了映射的组件

需要注意的是，export default routers 必须写在文件底部，而且后面还需要接一空行，否则无法通过 ESlint 语法验证



然后 main.js 也需要修改：

```
import Vue from 'vue'
import VueRouter from 'vue-router'
import routers from './routers'
import App from './App'

Vue.use(VueRouter)

const router = new VueRouter({
  mode: 'history',
  routes: routers
})

new Vue({
  el: '#app',
  router,
  render: h => h(App)
})
```
在创建的 router 对象中，如果不配置 mode，就会使用默认的 hash 模式，该模式下会将路径格式化为 #! 开头。

添加 mode: 'history' 之后将使用 HTML5 history 模式，该模式下没有 # 前缀，而且可以使用 pushState 和 replaceState 来管理记录。

关于 HTML5 history 模式的更多内容，可以参考官方文档：[https://router.vuejs.org/zh-cn/essentials/history-mode.html](https://router.vuejs.org/zh-cn/essentials/history-mode.html)





## 嵌套路由

在这个实例中，为了加深项目层级，App.vue 只是作为一个存放组件的容器：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img19.png)

其中 `<router-view>` 是用来渲染通过路由映射过来的组件，当路径更改时，'<router-view>' 中的内容也会发生更改

上面已经配置了两个路由，当打开 http://localhost:8080 或者 http://localhost:8080/home 的时候，就会在 <router-view> 中渲染 home.vue 组件



home.vue 是真正的父组件，first.vue、login.vue 等子组件都会渲染到 home.vue 中的 `<router-view>`
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img20.png)

如此一来，就需要在一级路由中嵌套二级路由，修改 routers.js

```
import Home from './components/home.vue'
import First from './components/children/first.vue'
import Login from './components/children/login.vue'

const routers = [
  {
    path: '/',
    component: Home,
　　 children: [
　　　{
　　　　path: '/',
 　　　 component: Login
　　  }
　　]
  },
  {
    path: '/home',
    name: 'home',
    component: Home,
    children: [
      {
        path: '/',
        name: 'login',
        component: Login
      },
      {
        path: 'first',
        name: 'first',
        component: First
      }
    ]
  }
]

export default routers
```
在配置的路由后面，添加 children，并在 children 中添加二级路由，就能实现路由嵌套

配置 path 的时候，以 " / " 开头的嵌套路径会被当作根路径，所以子路由的 path 不需要添加 " / "





## 使用 '<router-link>' 映射路由

home.vue 中引入了 header.vue 组件，其中含有导航菜单

当点击导航菜单的时候，会切换 home.vue 中 `<router-view>` 中的内容

这种只需要跳转页面，不需要添加验证方法的情况，可以使用 '<router-link>' 来实现导航的功能：
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img21.png)


在编译之后，'<router-link>' 会被渲染为 '<a>' 标签， to 会被渲染为 href，当 '<router-link>' 被点击的时候，url 会发生相应的改变

如果使用 v-bind 指令，还可以在 to 后面接变量，配合 v-for 指令可以渲染导航菜单



如果对于所有 ID 各不相同的用户，都要使用 home 组件来渲染，可以在 routers.js 中添加动态参数：
```
{
    path: '/home/:id',
    component: Home
}
```
这样 "/home/user01"、"/home/user02"、"/home/user03" 等路由，都会映射到 Home 组件

然后还可以使用 $route.params.id 来获取到对应的 id





## 编程式导航

实际情况下，有很多按钮在执行跳转之前，还会执行一系列方法，这时可以使用 this.$router.push(location) 来修改 url，完成跳转
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img22.png)

![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img23.png)


push 后面可以是对象，也可以是字符串：

```
// 字符串
this.$router.push('/home/first')

// 对象
this.$router.push({ path: '/home/first' })

// 命名的路由
this.$router.push({ name: 'home', params: { userId: wise }})
```




## 前车之鉴

在学习的过程中，遇到一个困扰许久的问题，大概是从 first 组件跳转回 login 之后，无法再跳转回去。但是 url 已经被修改，刷新页面也能正常显示。
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img24.png)


这是因为我在 first.vue 组件中的 data 里面没有写 return
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img25.png)
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img26.png)

在 vue 组件中，data 必须写为函数，且需要用 return 来返回参数。但是当 data 为空时，即使不写 return 也不会报错，所以导致了上面的问题。

转载自：[Vue 爬坑之路（三）—— 使用 vue-router 跳转页面](http://www.cnblogs.com/wisewrong/p/6277262.html)