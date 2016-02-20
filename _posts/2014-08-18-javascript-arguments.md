---
layout: post
title: Javascript 基本类型扩充功能
date: 2014-08-18 09:12:22.000000000 +08:00
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
<p>Javascript中可以自定义某种功能，并把它加到基本类型中去来方便自己的程序。比如我想给函数加一个功能，是每次给prototype添加方法时用更方便的方法。那么我们就可以自定义这个方法，也就是基本类型功能的扩展。</p>
<p>给Function.prototype添加方法将使得所有的函数都将可以使用这个方法：</p>
<pre>Function.prototype.method = function(name, func) {
    if(!this.prototype[name]) {
        this.prototype[name] = func;
    }
    return this;
}</pre>
<!--more-->
<p>这样在以后添加方法时就不用那么麻烦了，只需要**.method(name, function(){}); 就可以了。如果是正常来写，就要写成**.prototype.name = function() {}。这个扩充功能还有很多应用的地方，比如数字取整：</p>
<pre>Number.method("integer", function(){
    return Math[this &lt; 0 ? "ceil" : "floor"](this);
});

(-14 / 3).integer(); // -4, not -5</pre>
<p>这样就可以更方便的取整了。</p>
