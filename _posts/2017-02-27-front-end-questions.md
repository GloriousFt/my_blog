---
layout: post
title: "前端方向的一些零散面试小问题"
date: 2017-02-27 14:34:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Interview
- Front-end
meta:
  _edit_last: '1'
---

以下是博主自己总结的一些问题:

## 1. HTTP与HTTPS的区别

* HTTP是超文本传输协议,信息是明文传输,HTTPS则是具有安全性的SSL加密传输协议.
* HTTP和HTTPS使用的是完全不同的连接方式,用的端口也不一样,前者是80,后者是443.
* HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份.

## 2. HTTP的POST与GET具体区别

显而易见,POST是发送数据,GET是请求数据,但是其实两者都可以发送数据与接收数据.

* POST请求不会被缓存,GET会被缓存.
* POST请求如果带数据是不会显示到URL上的,而GET会显示在URL上,GET的安全性较差,不可用于身份认证登陆.
* POST对数据长度无限制,而GET有限制.

## 3. Javascript与传统面向对象语言有何不同

Javscript是一种原型语言,类的概念在javascript中是过时的.虽然它可以模拟类这种设计方式,包括实现继承,多态等,但是它本身的设计方式是组合方式.

## 4. Javascript函数方法call和apply区别

它们都是调用一个对象的一个方法,但参数不同,call是传指定参数,而apply是传参数数组.

## 5. CSS3一些新特性

边框border,阴影box-shadow,动画rotate,盒模型box,文字效果word-wrap,透明度opacity等.

## 6. Ajax原理

异步js与xml,不需要重载html,只对某部分进行异步更新.
```javascript
var xmlhttp = new XMLHttpRequest();
xmlhttp.open(method, url, true);
xmlhttp.send();
```

## 7. 一些前端优化方法

* 减少HTTP请求数,合理设置HTTP缓存.
* 样式引用放到<head>中
* 减少DOM操作数量
* 减少DOM节点数量
* 避免重复请求资源
* 使用<link>代替@import,否则相当于将css置底
* 资源压缩

## 2.TO BE CONTINUED