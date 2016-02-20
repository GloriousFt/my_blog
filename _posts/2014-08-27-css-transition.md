---
layout: post
title: CSS Transition的触发与实现淡入淡出效果
date: 2014-08-27 09:57:45.000000000 +08:00
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
<p>CSS3里有一个新东西，叫做transition，它可以实现许多属性改变时的动画效果。比如width改变，shadow改变等等。他有几种浏览器兼容性写法（以0.6秒动画为例）：</p>
<pre> -webkit-transition: 0.6s;
 -moz-transition: 0.6s;
 -ms-transition: 0.6s;
 -o-transition: 0.6s;
 transition: 0.6s;</pre>
<p>IE要在10或以上才可以，其它主流浏览器基本都支持。上面的CSS样式写在某个类里面，当某些属性改变时，这些改变就会以0.6s长的动画来显示效果。那么哪些效果才可以触发transition的效果呢？</p>
<!--more-->
<table class="animatable-properties" style="color: #000000;">
<tbody>
<tr>
<th>Property Name</th>
<th>Type</th>
</tr>
<tr>
<td>background-color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>background-position</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-repeatable-list">repeatable list</a> of <a href="http://www.w3.org/TR/css3-transitions/#animtype-simple-list">simple list</a> of <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>border-bottom-color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>border-bottom-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>border-left-color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>border-left-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>border-right-color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>border-right-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>border-spacing</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-simple-list">simple list</a> of <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>border-top-color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>border-top-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>bottom</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>clip</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-rect">rectangle</a></td>
</tr>
<tr>
<td>color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>font-size</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>font-weight</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-font-weight">font weight</a></td>
</tr>
<tr>
<td>height</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>left</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>letter-spacing</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>line-height</td>
<td>as either <a href="http://www.w3.org/TR/css3-transitions/#animtype-number">number</a> or <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>margin-bottom</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>margin-left</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>margin-right</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>margin-top</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>max-height</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>max-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>min-height</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>min-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>opacity</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-number">number</a></td>
</tr>
<tr>
<td>outline-color</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-color">color</a></td>
</tr>
<tr>
<td>outline-width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>padding-bottom</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>padding-left</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>padding-right</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>padding-top</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>right</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>text-indent</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>text-shadow</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-shadow-list">shadow list</a></td>
</tr>
<tr>
<td>top</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>vertical-align</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>visibility</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-visibility">visibility</a></td>
</tr>
<tr>
<td>width</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-lpcalc">length, percentage, or calc</a></td>
</tr>
<tr>
<td>word-spacing</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-length">length</a></td>
</tr>
<tr>
<td>z-index</td>
<td>as <a href="http://www.w3.org/TR/css3-transitions/#animtype-integer">integer</a></td>
</tr>
</tbody>
</table>
<p>表来源于<a title="W3C Transition" href="http://www.w3.org/TR/css3-transitions/#animtype-visibility" target="_blank">W3C Transition</a></p>
<p>当上面的属性改变时，都会触发transition 的效果。那transition一共有多少种变化效果呢？这得从transition的具体用法来看。</p>
<pre>transition-property
transition-duration
transition-timing-function
transition-delay</pre>
<p>第一个是定义transition的触发属性，也就是设定哪些属性改变时，transition生效，默认是all，也就是上边任何列出的属性变动时，都会触发transition效果。</p>
<p>第二个是定义transition效果的时间，最开始的例子就是0.6s的时间，比如改变一个元素的长度，想让他慢慢用时2s时间来变长，那就把duration设定为2s。</p>
<p>第三个是定义效果变化时间的速率，这个的实现较为复杂，但从使用者来说简单易用，它提供了几种速率变化方式：</p>
<ol>
<li>linear，匀速变化。</li>
<li>ease，逐渐变慢，默认值就是这个。</li>
<li>ease-in，加速变化。</li>
<li>ease-out，减速变化。</li>
<li>ease-in-out，先加速再减速。</li>
<li>cubic-bezier，自定义变化函数。</li>
</ol>
<p>其实每种变化函数都是用贝塞尔曲线来计算的，有兴趣的可以看<a title="Bézier_curve" href="http://en.wikipedia.org/wiki/Bézier_curve" target="_blank">这里</a>。这个东西没研究过，感觉十分复杂。这里的变化速率就是指在规定的duration时间内，动画变得快慢。其中ease就是默认的值。</p>
<p>第四个是定义执行transition效果前要延迟多少时间。比如设置2s，就是在执行变化前要延迟2秒钟。默认是0。</p>
<p>假如想定义多个属性的变化效果，中间用 “ , ” 隔开就可以，如：</p>
<pre class="css" style="color: #839496;"><span class="rules" style="color: #000000;">    <span class="rule"><span class="attribute">-moz-transition</span>:<span class="value"> background <span class="number">0.5</span>s ease-in 1s,color <span class="number">0.3</span>s ease-out 1s;</span></span>
    <span class="rule"><span class="attribute">-webkit-transition</span>:<span class="value"> background <span class="number">0.5</span>s ease-in 1s,color <span class="number">0.3</span>s ease-out 1s;</span></span>
    <span class="rule"><span class="attribute">-o-transition</span>:<span class="value"> background <span class="number">0.5</span>s ease-in 1s,color <span class="number">0.3</span>s ease-out 1s;</span></span>
    <span class="rule"><span class="attribute">transition</span>:<span class="value"> background <span class="number">0.5</span>s ease-in 1s,color <span class="number">0.3</span>s ease-out 1s;</span></span></span></pre>
<p>下面说一个常用的效果，淡入淡出的实现。</p>
<p>通常隐去一个元素用的较多的就是display：none，虽然opacity：0可以让元素不见，但其实它还是存在于页面上的，如果操作不当还会挡住用户鼠标等事件的抓取。visibility属性也可以隐去一个元素，可能还有一些别的方法，但这几种对我来说是较为常用的。这些具体的区别先不说，我们就来看他们怎么与transition搭配来实现淡入淡出。</p>
<p>用display：none的方法隐去一个元素，当条件触发时，我们再用display：block把它显示出来。这种方法可以实现“快入快出”，但不会触发transition效果，opacity可以触发transition效果，但是隐去之后那个元素就要显示在页面上，只不过为透明，这样方法也不好。visibility直接设置visible和hidden是不会出发transition效果的，但我们看到上面的适用属性里就有visibility，查看W3C说明，原来是要用贝塞尔曲线自定义timing，直接设置visible，hidden不可以，直接设置0，1的话CSS又不识别。那我们把他们放到一起组合着用会怎么样呢。</p>
<pre>.nav-bar {
   -webkit-transition: 0.6s;
   -moz-transition: 0.6s;
   -ms-transition: 0.6s;
   -o-transition: 0.6s;
   transition: 0.6s;
   visibility: 0;
   opacity: 0;
}</pre>
<p>先这样初始化一个元素，然后当条件触发时，我们用Javascript或JQuery给它加上新的CSS即 “visibility: 1; opacity:1”。这样就很好的实现了transition的淡入淡出效果。如果换成display：none和opacity：0的方法呢，实验后，发现会出现transition的效果，但是极不稳定，有时候会正常显示，更多时候是直接出现或抖动。但还有个方法可以解决，就是用SetTimeout函数来延迟执行display：block后的属性操作，比如这里就是设置opacity：1，这样就可以实现了淡入淡出。但就个人来言，我用的较多的还是visibility和opacity的方法。</p>
<p>能实现淡入淡出的一定还有很多方法，大不了直接上Javascript硬写。不过有CSS3提供的这么好的动画效果，干嘛不用呢。</p>
<p>Reference：<a title="CSS3 transition" href="http://www.w3.org/TR/css3-transitions/#animtype-visibility" target="_blank">http://www.w3.org/TR/css3-transitions/#animtype-visibility</a></p>
