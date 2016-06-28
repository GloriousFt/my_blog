---
layout: post
title: "ECMAScript中Number原始类型变量"
date: 2016-06-28 17:03:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript
meta:
  _edit_last: '1'
---
我们都知道,在Js中,变量都是弱类型的,一共有5种变量类型,Number就是其中一个.

这篇文章将会对Number类型进行一些细致的讲解.

Number可以表达十进制,八进制和十六进制,其中:

**八进制表达为数字前加"0":**

```javascript
  var a = 010; // a = 8
```

**十六进制表达为数字前加"0x":**

```javascript
  var b = 0x10; // b = 16
```

<!--more-->

对于浮点数来说,除了常有的小数点表达以外,还有科学技术法的表达方法:

```javascript
  var c = 2.3e8; // c = 230000000
```

它是遵照IEEE 754标准来进行64位存储的.

Number还有一些**特殊值**

```javascript
  Number.POSITIVE_INFINITY //表示无穷大
  Number.NEGATIVE_INFINITY //表示无穷小
  Number.MAX_VALUE //表示最大值
  Number.MIN_VALUE //表示最小值
  NaN //表示非数字
  Infinity //表示正无穷
  -Infinity //表示负无穷
```

从上面可以看到,Number也是对象,它也是由原始类型Number的引用类型.那就是说,它有自己的一些方法:

```javascript
  var num = new Number(100); // num = 100
  num.valueOf(); // 得到num的原始值
```

Number还有toFixed, toPrecision等一些方法.
