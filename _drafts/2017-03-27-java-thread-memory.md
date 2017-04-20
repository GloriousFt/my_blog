---
layout: post
title: "Java中的线程与内存(一)-线程工作的内存环境"
date: 2017-03-27 20:36:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Java
- Thread
meta:
  _edit_last: '1'
---
要说清楚Java线程的工作原理,得先明白Java的内存模型.

###Java线程内存模型
在Java中,除了主内存以外,每个线程在工作时还有一个专有的工作内存.工作内存中的变量数据其实就是主内存中某一部分的一个拷贝,每个线程的工作其实都是操作工作内存.

