---
layout: post
title: 'display: inline-block的空隙－css不起眼的问题（二）'
date: 2015-01-07 10:07:48.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- CSS
meta:
  _edit_last: '1'
---
<p>在chrome等浏览器下，两个并列元素用display：inline-block的方法实现时，经常会出现一个空隙，而查看元素时又常常什么都没有，这是怎么回事呢？</p>
<p>其实，这是代码空格符和空行引起的，解决方法就是将父元素font-size设置成0px就可以了，这样就会消除那些空隙。</p>
