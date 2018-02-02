---
title: Vue 爬坑之路（八）—— 使用 Echarts 创建图表

date: 2017-9-21
categories:
- Vue
tags:
- Vue
---

在后台管理系统中，图表是一个很普遍的元素。目前常用的图标插件有 charts,  Echarts, highcharts。这次将介绍 Echarts 在 Vue 项目中的应用。



## 安装插件

使用 cnpm 安装 Echarts

```cnpm install echarts -S```

和之前介绍的 axios 类似，echarts 也不能通过 Vue.use() 进行全局调用

通常是在需要使用图表的 .vue 文件中直接引入

```import echarts from 'echarts'```


也可以在 main.js 中引入，然后修改原型链

```Vue.prototype.$echarts = echarts```

然后就可以全局使用了

```let myChart = this.$echarts.init(document.getElementById('myChart'))```


## 创建图表

首先需要在 HTML 中创建图表的容器
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img53.png?Expires=1517565094&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=2uAZA2gbAwgAhm3PAmcgumkqGOw%3D)


需要注意的是，图表的容器必须指定宽高，也就是说 width，height 不能使用百分比，只能用 px

这样确实不够灵活，不过我们可以用 js 来改变 width 和 height，使图表容器能够自适应，具体的实现请往后看
![](http://xie-blog.oss-cn-beijing.aliyuncs.com/blogImg/img54.png?Expires=1517565106&OSSAccessKeyId=TMP.AQFykThi91U598dTrJc_9IBPer-xtxfyUZ278vOwz9sVKvVjdZC6hsnJbSZiADAtAhQ8dmqdGscv8Mq8gp6YtjbW3Tmz3wIVALsOiURiHSXhx6xtRna9_tLmtbDC&Signature=w7n0HFkc2cHNJITWfpsAON%2F9RUQ%3D)

然后在 mounted 中创建图表，具体的配置参考官方文档，这里不再赘述


## 按需引入

上面引入的 echarts 包含了所有图表，但有时候我们只需要一两个基本图表，这时候完整的 echarts 就显得累赘

假如只需要创建一个饼图，那么可以这么做：

```
  // 引入基本模板
  let echarts = require('echarts/lib/echarts')
  // 引入饼图组件
  require('echarts/lib/chart/pie')
  // 引入提示框和图例组件
  require('echarts/lib/component/tooltip')
  require('echarts/lib/component/legend')
```
可以按需引入的模块列表见 [https://github.com/ecomfe/echarts/blob/master/index.js](https://github.com/ecomfe/echarts/blob/master/index.js)



## 适应容器

上面说过，图表的容器必须固定宽高，这确实是一个比较反人类的设定

为了解决这个问题，需要给图表容器外面再加一个容器，而这个外容器的宽高可以适应页面。上面的 <div class="charts"> 就是这样的外容器

```
let chartBox = document.getElementsByClassName('charts')[0]
let myChart = document.getElementById('myChart')

// 用于使chart自适应高度和宽度,通过窗体高宽计算容器高宽
function resizeCharts () {
  myChart.style.width = chartBox.style.width + 'px'
  myChart.style.height = chartBox.style.height + 'px'
}
// 设置容器高宽
resizeCharts()

let mainChart = echarts.init(myChart)
mainChart.setOption(options)
```
当页面加载的时候，将外容器的宽高，赋给图表容器

但这只是解决了页面加载时的自适应问题

如果在页面加载之后，仍需要图表自适应宽高，就需要用到 echarts 的[媒体查询](http://echarts.baidu.com/tutorial.html#%E7%A7%BB%E5%8A%A8%E7%AB%AF%E8%87%AA%E9%80%82%E5%BA%94)

转载自：[Vue 爬坑之路（八）—— 使用 Echarts 创建图表](https://www.cnblogs.com/wisewrong/p/6558001.html)