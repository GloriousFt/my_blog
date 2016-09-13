---
layout: post
title: "浏览器对CSS的样式权重Specificity计算"
date: 2016-09-13 14:50:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- SPECIFICITY
- CSS
meta:
  _edit_last: '1'
---

[上一篇](http://litianyi.cc/technology/2016/08/18/css-loading-sequence/)讲了对CSS样式浏览器的渲染结果是什么,但是为什么是这样,为什么有的样式定义优先级就高一些,其实,浏览器是通过specificity计算来决定的.

## 1.specificity是什么

一个页面元素的样式是怎么计算出来的呢?

浏览器先对外部样式,内部样式和行内样式进行解析形成语法树,然后通过匹配规则把匹配到一个元素的样式赋予这个元素.

然而有时候会出现这种问题,内部样式对某种样式属性进行了定义,外部样式也对某种样式进行了定义,那么浏览器该对这个属性进行哪种取值.如果选择器不相同,那么又该要哪种选择器优先呢?

这时就要specificity来决定了.Specificity就是对选择器进行权重比较的一个值.

## 2.specificity如何计算

specificity的值是这样计算的:

* 计数选择器中ID选择器的个数(如: #element { ... }), 让 a = 计数值.
* 计数选择器中类选择器,属性选择器,伪类选择器的个数(如: .class1, [id=1], :hover), 让 b = 计数值.
* 计数选择器中类型选择器(标签选择器)和伪元素选择器的个数(如: h1, :before), 让 c = 计数值.

<!--more-->

其他的通用选择器(*)和组合符号(+,>,~,' ')等都不做计数,":not"也不做计数,但是":not"其内部的选择器依然计数.

如(来自[W3C Recommendation](https://www.w3.org/TR/selectors/#FLEX)):

```html
*               /* a=0 b=0 c=0 -> specificity =   0 */
LI              /* a=0 b=0 c=1 -> specificity =   1 */
UL LI           /* a=0 b=0 c=2 -> specificity =   2 */
UL OL+LI        /* a=0 b=0 c=3 -> specificity =   3 */
H1 + *[REL=up]  /* a=0 b=1 c=1 -> specificity =  11 */
UL OL LI.red    /* a=0 b=1 c=3 -> specificity =  13 */
LI.red.level    /* a=0 b=2 c=1 -> specificity =  21 */
#x34y           /* a=1 b=0 c=0 -> specificity = 100 */
#s12:not(FOO)   /* a=1 b=0 c=1 -> specificity = 101 */
```

以此specificity的值来决定用哪个选择器的值来决定相同样式的属性.

至于样式定义的优先级上一篇已经讲过了,不再赘述.