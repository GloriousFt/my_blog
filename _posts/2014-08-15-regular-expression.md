---
layout: post
title: "正则表达式"
date: 2014-08-15 09:59:17.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- RegularExpression
meta:
  _edit_last: '1'
---
<p>做Js压缩或者文件内容压缩时经常会用到字符串处理与字符替换，较为常用的方法是正则表达式的方法。</p>
<p>试想一种情况，你想在一大堆数据中寻找特定的文字，比如，作为一个数据监管员，你想查看数据中有没有敏感字符，于是你想找“China”的数据。那正则表达式就是/bChina/b/g。</p>
<p>／b是表示寻找字符串的，中间的China就是要寻找的字段。最后面的/g表示这是全局查找，出现多少次都算找到，不加这个就只能算查找一次第一次出现的China。这种有特殊意义的字符就是元字符，当然这只是一点点正则表达式的内容，下面是一些常用的语法。</p>
<!--more-->
<p>"*" ，表示匹配0到多个字符，如“r*t”，表示寻找r与t中间夹着任意r的字符串，如root，rrot，rt这样的字符串。</p>
<p>"."，表示匹配单个字符，上例则只能找到rt，rot这种字符串。</p>
<p>"?"，表示匹配一个或0个字符，可以找到rt，t这样的字符串。</p>
<p>"+"，表示匹配一个到多个的字符，不可以找到t，但可以找到rot，rrot这样的字符串。</p>
<p>"{n}"，表示匹配到n个字符，{3}就只能找到r与t之间隔2（n－1）个字符的字符串，如rrrot。</p>
<p>"{n, m}"，表示匹配n到m个字符，{2, 3}就可以找到r与t隔1到2个的字符串，如rorot，rrrooot。</p>
<p>那寻找数字而不是字符的正则表达式是什么样的呢，/d是表示数字的正则式。比如我们想寻找一条手机号还怎样写正则式呢？/(?/d/d/)?-/d/d/d/d/d/d/d/d是一种较长的写法，/(与/)是转义字符，不然直接写( )会被当作元字符来处理。这个就是寻找类似于010-21392183或者(012)-23812738这样的字符串。这个可以简写为/?(0/d{2}/)?-/d{8}，/d{n}表示连续几个数字的查找。</p>
<p>当然正则表达式还有很多语法，这里这个链接是介绍的较为全面的了：<a title="30分钟正则表达式学习" href="http://http://www.cnblogs.com/deerchao/archive/2006/08/24/zhengzhe30fengzhongjiaocheng.html" target="_blank">dearchao的blog</a>。</p>