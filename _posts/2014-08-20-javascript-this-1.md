---
layout: post
title: Javascript 中函数调用方法与this用法(一)
date: 2014-08-20 08:28:19.000000000 +08:00
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
<p>Javascript中的this的确是比较灵活，与传统面向对象编程不同，Javascript的函数有很多调用方法，这就使得this的用法也有很多变化。在学习this用法前，最好先了解一共有哪些函数调用方法，这样学习起来就轻松很多了。</p>
<p>1. 函数调用模式</p>
<p>这是最为普通的调用模式，函数只是作为函数来调用。</p>
<pre>var number = 1
function func() {
    console.log(this.number);
}
func(); // 1</pre>
<p>这个直接调用函数func，console上一定会显示1，因为此时this指的就是window。换一种方法写：</p>
<pre>function func() {
    var number = 0;
    console.log(this.number);
}
func();// undefined</pre>
<!--more-->
<p>这种方法下，调用func后console显示的是undefined。</p>
<p>2. 对象方法调用模式</p>
<p>函数作为对象的一个方法被调用。</p>
<pre>var class1 = {
    number : 1,
    func : function() {
        console.log(this.number);
    }
};
class1.func(); // 1</pre>
<p>此时this就是指它的上级对象，也就是class1对象。</p>
<p>3. 构造器调用模式</p>
<p>将函数写成构造函数，此时this很易出问题。按照默认约定，构造函数都要开头大写。</p>
<pre>var Car = function(name) {
    this.name = name;
}
var car = new Car("1");
console.log(car.name);// "1"</pre>
<p>此时this指的就是Car这个对象，它有一个属性是name。但如果不用new来定义car对象，比如直接写成var car ＝ Car("1")；那么car里面是不会有name属性的，同时全局window还会出现name属性，这就是因为this此时去指了全局变量window。而不是car，所以写构造函数时一定要注意用new，同时加以开头大写来区分。</p>
<p>4. Apply调用模式</p>
<p>Apply方法可以有两个参数，第一个是让我们写将this绑定到的对象，第二个参数是参数数组。</p>
<pre>number = 0;
function func() {
    console.log(this.number);
}
var x = {};
x.number = 1;
x.method = func;
x.method.apply();// 0
x.method.apply(x);// 1</pre>
<p>apply方法成功将this的值改变，为空的时候就是全局变量。</p>
