---
title: Ajax请求返回数据渲染Boostrap整理
date: 2020-04-23 11:22:49
categories: [编程]
tags: [Flask,Bootstrap,Ajax]
---

1.后端的数据怎么传给前端

   1、flask的render_template同步渲染

​      后端渲染页面的时候传递数据，前端用特殊的语法， 比如 {{变量}}

​      优点：开发简单，直接jinjia2语法渲染，后端渲染，性能呢个比较好

​      缺点：更新数据需要刷新整个页面，体验差

​    2、ajax 异步更新

​       render_template 没有变量，或只有少数变量

​       还有一个数据结构，通过js获取数据，渲染页面

​       优点：异步获取数据，局部更新页面 体验好

​       缺点： 首屏比较慢

2、前端的数据，怎么给后端

​    1、form 表单直接跳转

​       同步提交

​    2、ajax

​       异步提交，用户无感知。



參考資料：

https://www.cnblogs.com/nopnog/p/7094454.html