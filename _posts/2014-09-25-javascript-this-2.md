---
layout: post
title: Javascript 中函数调用方法与this用法(二)
date: 2014-09-25 08:57:41.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- JavaScript
meta:
  _edit_last: '1'
---
<p>在第一篇里简单介绍了函数的四种调用方法，这里再来详细说一下this的意义与用法。</p>
<p>this具体是指什么，在java权威指南中如此定义：函数在被调用时会拥有一个值，就是本次调用的上下文（context），这个就是this关键字的值。“如果函数挂载在一个对象上，作为对象的一个属性，就称它为对象的方法。当通过这个对象来调用函数时，该对象就是此次调用的上下文，也就是该函数的this的值。”</p>
<p>和变量不一样，this没有作用域的限制，当该函数没有上下文时，函数中的this通常是undefined或者是全局的window。比如：</p>
<pre>var a = 0;
function s() {
    var a = 1;
    console.log(this.a);
}
var b = s;
b();</pre>
<!--more-->
<p>输出的是0。因为s是这个函数是作为函数来调用的，所以this是window。</p>
<p>如果函数出现嵌套的情况，那么内部嵌套的函数this是指什么呢。同理，如果是作为方法，它是指调用它的对象，如果是作为函数调用，它就是window或者undefined。如果想访问外部函数的this，一般都用self或者that（非关键字）来代替this。</p>
<pre>var obj = {
    func : function() {
        var that = this;
        funcInside();
        function funcInside() {
            console.log(this);// window 
            console.log(that);// obj
        };
    }
};

obj.func();</pre>
<p>可以看出来，一个是window,一个是obj。</p>
<p>&nbsp;</p>
