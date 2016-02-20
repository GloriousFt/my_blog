---
layout: post
title: "算法基础-插入排序之直接插入排序"
date: 2014-07-29 03:22:13.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Algorithm
- StraightInsertionSort
meta:
  _edit_last: '1'
---
<h3>稳定性：</h3>
<p>直接插入排序算法(Straight Insertion Sort Algorithm)是一种稳定的排序算法，何为稳定？就是假如被排序数据中有相同值的两个数据单元，在排序后他们的前后关系不改变就是稳定。有的算法就会改变，在后面的算法中将会遇到。</p>
<h3><strong>原理：</strong></h3>
<p>直接插入排序算法是插入排序算法最基本的一种，它是在每次循环中将未排序的第一个数据直接插入到已排序的数据中，然后依次调整已排序数据的位置，算法较为简单直接。为判断处理越界及不单独另申请内存空间，这里使用“哨兵”来处理。即让Array[0]的位置为哨兵，将每次暂存的数据放到这里，并且在判断越界时也十分巧妙，假设当前判断数据为最小（最大）数据，当他判断到最后一个位置时，它将于自己进行比较，这样必然会不满足条件而退出当前循环。从而省去了每次都要判断是否越界。</p>
<p>因此下例排序为1到N个元素排序，0号元素为哨兵。</p>
<h3>算法：</h3>
<pre>
for(int i = 2;i &lt; 100; i++) {
    if(array[i] &lt; array[i - 1]) {
        array[0] = array[i];
        for(int j = i; array[j - 1] &gt; array[0]; j--) {
            array[j] = array[j - 1];
        }
        array[j] = array[0];
    }
}</pre>
<!--more-->
<h3>复杂度：</h3>
<p>考虑循环最多次情况，为N * N次，时间复杂度为o(n^2)，空间复杂度为o(1)。</p>
<h3>优缺点：</h3>
<p>算法稳定，空间占用小，无需另新申请内存空间。但算法效率相对其他算法并不是十分高效。</p>
<h3>测试：</h3>
<p>测试为Javascript所写，测试环境为：处理器  双核 2.6 GHz Intel Core i5。</p>
<p>测试数据100000个。多次测试后平均结果为5680 ms。</p>
