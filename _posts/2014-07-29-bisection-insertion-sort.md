---
layout: post
title: "算法基础-插入排序之折半插入排序"
date: 2014-07-29 10:19:24.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Algorithm
- BisectionInsertionSort
meta:
  _edit_last: '1'
---
<h3>原理：</h3>
<p>折半插入排序与直接插入排序有些相似，都有相同的查找后移位操作，不同的是，在寻找移位的位置时，折半插入是用二分法来进行查找，而不是把每个数都进行比较。直接插入排序是将数据分成有序和待排序的两组数据，这里就是用二分法查找有序数据中待排序数据的位置。二分法是，设置一个头坐标，设置一个尾坐标，再设置一个中间值，大小就等于头加尾和的一半，每次用被查找数与数据中中间值位置的数据比较，若小于中间值的数据则将尾坐标设置为中间值减1，反之将头坐标设置为中间值加1。具体二分法在后面查找算法中会提到，这里要注意的是，二分法只适用于已排序的数据。</p>
<h3>算法：</h3>
<pre>int low, mid, high;
for(int i = 2;i &lt; count;i++) {
    low = 1;
    high = i - 1;
    Array[0] = Array[i];
    while(low &lt;= high) {
        mid = (low + high) / 2;
        if(Array[mid] &gt; Array[0])
            high = mid - 1;
        else
            low = mid + 1; 
    }
    for(int j = i;j &gt; high + 1;j--) {
        Array[j] = Array[j - 1];
    }
    Array[j] = Array[0];
}</pre>
<!--more-->
<h3>稳定性：</h3>
<p>二分法是查找方法，因此折半插入排序与直接插入排序在稳定性上没有差别，都是稳定的算法。</p>
<h3>复杂度：</h3>
<p>二分法查找要比直接查找提高不少效率，2^x = N，所以共进行了2logN次运算，再算上外层每个数据的排序，时间复杂度为o(N*2logN)，空间复杂度还是o(1)。</p>
<h3>优缺点：</h3>
<p>优点是效率较好，对比直接插入排序来说。</p>
<h3>测试：</h3>
<p>测试数据100000个。多次测试后平均结果为3700 ms。效率还是有较大提升。</p>
<h3>PS：每篇算法都是用Javascript进行的测试，环境为双核 2.6 GHz Intel Core i5。</h3>
