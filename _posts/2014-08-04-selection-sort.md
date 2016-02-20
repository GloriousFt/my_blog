---
layout: post
title: "算法基础－选择排序之直接选择排序"
date: 2014-08-04 03:45:56.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Algorithm
- SelectSort
meta:
  _edit_last: '1'
---
<h3>原理：</h3>
<p>选择排序的方法比较简单，每次循环一次后，相应位置的数据就是已排序好的数据，每次即可确定相应位置的数据。进行N－1次后，数据有序。具体先是从第一个位置开始，遍历全部数据找出最值，然后记录位置下标，将第一个数据或最后一个数据与之交换。共进行N－1次，达到排序目的。</p>
<h3>算法：</h3>
<pre>int pos;
for(int i = 0;i &lt; Array.length;i++) {
    pos = i;
    for(int j = i + 1;j &lt; Array.length;j++) {
        if(Array[j] &lt; Array[pos])
            pos = j;
    }
    if(pos != i) {
        swap(Array[pos], Array[i]);
    }
}</pre>
<!--more-->
<h3>稳定性：</h3>
<p>直接选择排序是不稳定的排序算法，假设数据：<span style="color: #ff0000;">4</span>，2，4，1，5。第一次排序后<span style="color: #ff0000;">4</span>与1交换位置，变成了1，2，4，<span style="color: #ff0000;">4</span>，5。</p>
<h3>优缺点：</h3>
<p>选择排序简单易行，十分易于实现，但效率不高，不管什么情况都要进行N^2次运算。</p>
<h3>复杂度：</h3>
<p>最外层循环是N次数据定位，里层是N－1次比较，所以时间复杂度就是o(N^2)，空间复杂度是o(1)。</p>
<h3>测试：</h3>
<p>100000的整型数据，升序排列平均用时6450 ms。</p>
<p>语言：Javascript。</p>
<h2>在线测试链接：<a title="排序算法测试" href="http://112.126.74.28/algorithms/index.html">112.126.74.28/algorithms/index.html</a></h2>
