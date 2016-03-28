---
layout: post
title: "几个雅虎前端优化法则(部分)"
date: 2016-03-28 15:35:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Frontend
meta:
  _edit_last: '1'
---
这里将遇到过的几个优化问题加以记录.

根据雅虎前端优化法则:

> **减少DOM元素数量**
> DOM元素数量越多,JavaScript遍历的就越久,会影响效率.
>

> **将HTTP请求次数尽可能降低**
> 浏览器打开网页,大部分时间都是在下载css,js,images等等.
>

> **预加载下个页面的资源**
> 这个想法跟计算机体系结构中的流水线思想很像,就是说可以预判用户下个页面,然后提前加载资源.
> 在用户空闲时加载js,css等,然后缓存.
>

<!--more-->
> **将css放到head标签里**
> 浏览器最先分析head标签,这样页面就可以分步加载css样式,加强用户体验.
>

> **避免使用css表达式**
> css表达式计算的频率太高,为了不降低性能尽量不要使用css表达式.
>

> **使用外部css和js**
> 外部的css和js可以缓存.
>

> **js和css文件应该尽量的小**
> js和css文件过大可能导致加载时间过长.
>