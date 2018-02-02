---
title: Vue 爬坑之路（六）—— 使用 Vuex + axios 发送请求
date: 2017-8-20
categories:
- Vue
tags:
- Vue
---

Vue 原本有一个官方推荐的 ajax 插件 vue-resource，但是自从 Vue 更新到 2.0 之后，官方就不再更新 vue-resource

目前主流的 Vue 项目，都选择 axios 来完成 ajax 请求，而大型项目都会使用 Vuex 来管理数据，所以这篇博客将结合两者来发送请求



## 前言：

Vuex 的安装将不再赘述，可以参考之前的博客[Vue 爬坑之路（四）—— 与 Vuex 的第一次接触]()

使用 cnpm 安装 axios

cnpm install axios -S

安装其他插件的时候，可以直接在 main.js 中引入并 Vue.use()，但是 axios 并不能 use，只能每个需要发送请求的组件中即时引入

为了解决这个问题，有两种开发思路，一是在引入 axios 之后，修改原型链，二是结合 Vuex，封装一个 aciton。具体的实施请往下看~



方案一：改写原型链

首先在 main.js 中引入 axios

import axios from 'axios'

这时候如果在其它的组件中，是无法使用 axios 命令的。但如果将 axios 改写为 Vue 的原型属性，就能解决这个问题

Vue.prototype.$ajax = axios

在 main.js 中添加了这两行代码之后，就能直接在组件的 methods 中使用 $ajax 命令


```
methods: {
  submitForm () {
    this.$ajax({
      method: 'post',
      url: '/user',
      data: {
        name: 'wise',
        info: 'wrong'
      }
   })
}
```




方案二：在 Vuex 中封装

之前的文章中用到过 Vuex 的 mutations，从结果上看，mutations 类似于事件，用于提交 Vuex 中的状态 state

action 和 mutations 也很类似，主要的区别在于，action 可以包含异步操作，而且可以通过 action 来提交 mutations

另外还有一个重要的区别：

mutations 有一个固有参数 state，接收的是 Vuex 中的 state 对象

action 也有一个固有参数 context，但是 context 是 state 的父级，包含  state、getters



Vuex 的仓库是 store.js，将 axios 引入，并在 action 添加新的方法


```
// store.js
import Vue from 'Vue'
import Vuex from 'vuex'

// 引入 axios
import axios from 'axios'

Vue.use(Vuex)

const store = new Vuex.Store({
  // 定义状态
  state: {
    test01: {
      name: 'Wise Wrong'
    },
    test02: {
      tell: '12312345678'
    }
  },
  actions: {
    // 封装一个 ajax 方法
    saveForm (context) {
      axios({
        method: 'post',
        url: '/user',
        data: context.state.test02
      })
    }
  }
})

export default store

```

注意：即使已经在 main.js 中引入了 axios，并改写了原型链，也无法在 store.js 中直接使用 $ajax 命令

换言之，这两种方案是相互独立的


在组件中发送请求的时候，需要使用 this.$store.dispatch 来分发

```
methods: {
  submitForm () {
    this.$store.dispatch('saveForm')
  }
}
```

submitForm 是绑定在组件上的一个方法，将触发 saveForm，从而通过 axios 向服务器发送请求


## 附录：配置 axios

上面封装的方法中，使用了 axios 的三个配置项，实际上只有 url 是必须的，完整的 api 可以参考使用说明

为了方便，axios 还为每种方法起了别名，比如上面的 saveForm 方法等价于：

axios.post('/user', context.state.test02)

完整的请求还应当包括 .then 和 .catch
```
.then(function(res){
  console.log(res)
})
.catch(function(err){
  console.log(err)
})
```

当请求成功时，会执行 .then，否则执行 .catch

这两个回调函数都有各自独立的作用域，如果直接在里面访问 this，无法访问到 Vue 实例

这时只要添加一个 .bind(this) 就能解决这个问题

```
.then(function(res){
  console.log(this.data)
}.bind(this))
```

转载自：[Vue 爬坑之路（六）—— 使用 Vuex + axios 发送请求](http://www.cnblogs.com/wisewrong/p/6402183.html)