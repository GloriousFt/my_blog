---
layout: post
title: Img的title和alt－HTML与CSS不起眼的问题（一）
date: 2014-10-27 10:30:07.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- CSS
- HTML
meta:
  _edit_last: '1'
---
<p>好久没来post了，最近实在太忙了，一人做着大量的工作，不过好处就是积累的更多的经验（我这样安慰自己）。下面来看看HTML与CSS中一些不起眼的细节吧。</p>
<p>Img中的alt标签用得较多，多是用来显示图片的替换文字的，比如图片无法正常显示时，若想加一些提示信息或者图片信息，就可以使用这个标签。而title属性也是实现类似的功能的，当用户将鼠标放到图片上的时候，图片上会浮出一个标签显示设置的相应内容。但他们确实是有区别的：</p>
<p>alt属性会被搜索引擎取到，也就是说做SEO的时候很有帮助。而title更多是从用户角度来看的，增强用户体验用的。在IE下两者显示功能几乎相同，但alt会被搜索引擎抓取。</p>
<p>当然上述只是我觉得不起眼的不同点，还有一些其他的不同。比如适用标签不同，alt只能用在img、area和input上，而title能用在很多标签上。还有显示的最大字符长度等。</p>
