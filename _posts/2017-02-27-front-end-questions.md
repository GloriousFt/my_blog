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

## 1.HTTP与HTTPS的区别

* HTTP是超文本传输协议,信息是明文传输,HTTPS则是具有安全性的SSL加密传输协议.
* HTTP和HTTPS使用的是完全不同的连接方式,用的端口也不一样,前者是80,后者是443.
* HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份.

## 2. HTTP的POST与GET具体区别

显而易见,POST是发送数据,GET是请求数据,但是其实两者都可以发送数据与接收数据.

* POST请求不会被缓存,GET会被缓存.
* POST请求如果带数据是不会显示到URL上的,而GET会显示在URL上,GET的安全性较差,不可用于身份认证登陆.
* POST对数据长度无限制,而GET有限制.

## 2.TO BE CONTINUED