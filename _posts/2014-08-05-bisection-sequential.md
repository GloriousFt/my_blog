---
layout: post
title: "算法基础-顺序查找和二分查找"
date: 2014-08-05 03:51:22.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Algorithm
- Bisection
- Sequential
meta:
  _edit_last: '1'
---
<h3>原理：</h3>
<p>这两种算法在查找中都较为常见，顺序查找就是从前向后查找匹配，平均查找长度为(1 + N) * N / 2 / N，最终为(N + 1)/2。二分查找是每次折半查找，其实相当于一个平衡二叉树，查找它的叶子的过程。平均查找长度就是约为log2(N + 1) - 1。它的前提必须是顺序存储结构，且是有序数据。开始时设置一个起点和终点，还有中间点，每次取中间点位置的数据与关键字比较，若大于关键字则把起点设置为中间点加一，若小于关键字则把终点设置为中间点减一，若相等则直接返回中间点坐标。</p>
<h3>算法：</h3>
<pre>int low, mid, high;
low = 0, high = Array.length - 1;
while(low &lt;= high){
    mid = (low + high)/2;
    if(Array[mid] &gt; key)
        low = mid + 1;
    else if(Array[mid] == key)
        return mid;
    else
        high = mid - 1;
}//Bisection Search
<!--more-->
int i;
for(i = Array.length - 1;Array[i] != key ;i--) {}
return i;
//Sequential Search</pre>
<h3>复杂度：</h3>
<p>时间复杂度，顺序查找为o(N)，二分查找为o(log2N)。</p>
<h3>测试链接（直接看Js文件就可以看到算法）：</h3>
<h2 style="font-weight: 500;"><a style="color: #428bca;" title="排序算法测试" href="http://112.126.74.28/algorithms/index.html">112.126.74.28/algorithms/index.html</a></h2>
