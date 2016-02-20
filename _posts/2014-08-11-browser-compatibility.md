---
layout: post
title: Hack 浏览器兼容性问题
date: 2014-08-11 15:07:37.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- BrowserCompatibility
- IEHack
meta:
  _edit_last: '1'
---
<p>Hack就是考虑兼容性针对各大浏览器写代码的过程，在css3出现以后，兼容性问题越来越明显，现在主要设计方案有两种叫法：优雅降级和渐进增强。</p>
<p>优雅降级 (Graceful Degradation) 就是在设计页面时就从最优css效果开始，逐渐向后的兼容老版本浏览器，它侧重先使用高级css样式达到最佳效果，对于不兼容的浏览器，采用“poor，but passable”的策略。</p>
<p>渐进增强(Progressive Enhancement)与优雅降级正好相反，它是优先兼容老版本浏览器，满足最基本功能，在设计时就先以较为基本的css样式为基准，然后向前增加新浏览器版本支持的效果。</p>
<p>了解了这两个概念，再来看Hack。</p>
<p>Css Hack是较为常用的一种，对于像IE6和IE7的老版本浏览器来说，Hack是非常必要的。IE6能优先识别 "*" 与 "_" , IE7优先识别*，因为现在几乎已经不单独区分IE6和IE7了，所以在写Css的时候都是加上 "*"。这个有什么用呢，看下面的一个例子。我们想在高级浏览器上显示带透明的效果，用RGBA方法，但是IE旧的浏览器明显不支持，那么我们用优雅降级的方法，在这个地方我们用白色背景来代替（其实filter可以实现）</p>
<pre>.opacity-class {
    background-color: rgba(0,0,0,0.5);
    *background-color: #ffffff;
}</pre>
<!--more-->
<p>这样，像Chrome这样的浏览器会解析到rgba的方法，然后背景设为了黑色0.5透明，而IE会优先解释第二句，将背景设为白色。</p>
<p>还有HTML头部引用的Hack，比如：</p>
<p>&lt;!--[if IE]&gt;&lt;!--您的代码--&gt;&lt;![endif]--&gt;</p>
<p>这个就可以区别开所有的IE浏览器，只有IE下才会执行这段代码。</p>
<p>还有一种选择器 Hack，它是来区别类的，比如IE6下<b>* html #demo {width:120px;}。</b></p>
<p>下面是对IE10之前浏览器Hack的方法（资料整理自网络搜索）：</p>
<div class="para">"\9"　例:"border:1px \9;".这里的"\9"可以区别所有IE和FireFox。（只针对IE9 Hack）</div>
<div class="para">"\0"　IE8识别，IE6、IE7不能。</div>
<div class="para">"*"　IE6、IE7可以识别.IE8、FireFox不能。</div>
<div class="para">"_"　IE6可以识别"_",IE7、IE8、FireFox不能。</div>
