---
layout: post
title: JS 的 delete 与 typeof 运算符
date: 2014-09-10 09:52:56.000000000 +08:00
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
<p>typeof是用来返回数据类型的一元运算符，比如typeof x或typeof(x)就是返回变量x的数据类型，下面是返回值列表：</p>
<pre>undefined     "undefined"
null          "object"
true,false    "boolean"
number,NaN    "number"
String        "string"
function      "function"
object        "object"</pre>
<!--more-->
<p>需要注意null的typeof返回的是“object”，NaN的返回是“number”。</p>
<p>delete运算符是删除对象属性或者数组元素的。如delete object.x或delete array[1]，下面有几个地方需要注意：</p>
<pre>var a = {x:1,y:2};
delete a ;这会返回false
delete a.x ;返回true

this.x = 1;
delete x;返回true，因为它是全局对象的属性。</pre>
<p>delete运算符的某些运算其实在不同ECMAScript5 模式下结果不同，比如上面删除x的时候，严格模式就会抛出异常，但普通模式就会返回true。具体的还要参考ECMAScript5的标准了。</p>
