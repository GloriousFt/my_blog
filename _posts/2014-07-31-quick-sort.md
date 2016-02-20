---
layout: post
title: "算法基础-交换排序之快速排序"
date: 2014-07-31 07:45:48.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Algorithm
- QuickSort
meta:
  _edit_last: '1'
---
<h3>原理：</h3>
<p>快速排序是排序中我比较喜欢的一个算法，效率高，十分常用。而且这个算法的思路也十分不错，每一次的遍历就可以排出一个确定其在数据中最终位置的数据，非常适合原始数据就比较无序的情况。以升序排列为例，整体思路是，</p>
<ol>
<li>设置两个变量，一个在头，一个在尾，不妨设成start与end。先从数据中选出一个关键数据－pivot（通常把数据中第一个数据就设为关键数据），再从右向左找，end自减，碰到一个比它小的，这时就把关键数据缓存起来，然后把找到的数据放到原关键数据的位置。</li>
<li>再从新位置start开始向右找，start自增，直到碰到一个比关键数据大的，再把这个数据放到之前的end位置。</li>
<li>重复向左找和向右找的过程，直到start和end相同，这时就把关键数据放到这个位置，它的左边一定都是比它小的，而右面一定都是比它大的。</li>
<li>此时完成一个数据的定位，通过递归，再进行此时关键数据左半边的递归，和右半边的递归，最后把全部数据都定位，完成排序。</li>
</ol>
<!--more-->
<p>看下面一个小例子：</p>
<p>数据假设为3，0，2，6，1。</p>
<ol>
<li>把3设为关键数据－pivot，开始时end为4（Array.length - 1），start为0，然后end自减，每次看Array[end]的值与关键数据3大小，此时3&gt;1，所以把1放到start位置，数据变为1，0，2，6，1。pivot＝3。</li>
<li>再向右寻找，start自增，看Array[start]与pivot的大小，1&lt;3，继续查找，0&lt;3，2&lt;3，6&gt;3，所以此时start＝3，把6放到end位置，数据变为1，0，2，6，6。</li>
<li>此时start ＝ 3，end＝4，再像左查找，end自减，发现start与end相同，此时停止pivot定位，把pivot放到start位置上，也就是1，0，2，3，6。此时就可以看到，3的左边都是比它小的数据，右边都是比它大的数据。</li>
<li>以上只是一次的定位，这时进行递归，把1，0，2看成一个新数据再次进行之前的操作，把6也进行同样操作，最终得到0，1，2，3，6。</li>
</ol>
<h3>算法：</h3>
<pre>void quickSort(int Array, int start, int end) {
    if(start &lt; end) {
        int pivotPos = partition(Array, start, end);
        quickSort(Array, start, pivotPos - 1);
        quickSort(Array, pivotPos + 1, end);
    }
}

int partition(int Array, int start, int end) {
    int pivot = Array[start];
    while(start &lt; end) {
        while(start &lt; end &amp;&amp; pivot &lt;= Array[end]) end--;
        Array[start] = Array[end];
        while(start &lt; end &amp;&amp; pivot &gt;= Array[start]) start++;
        Array[end] = Array[start];
    }
    Array[start] = pivot;
    return start;
}</pre>
<h3>稳定性：</h3>
<p>快速排序是不稳定的排序算法。假设一组数据5，3，3，2，<span style="color: #ff0000;">3</span>，7，第一次查找就会把标红的3放到5的位置。</p>
<h3>优缺点：</h3>
<p>快速排序的效率与原始数据排列有关，若原数据已然有序，则算法只需比较，递归会直接退出。但平均效率还是很好的。</p>
<h3>复杂度：</h3>
<p>假设数据逆序，那么就要进行N次排序才确定他最终位置。最坏需要进行N - 1次递归，所以最坏时间复杂度为o(N ^ 2)。当数据很乱的时候，时间复杂度最好为o(N * log2N)。递归就是栈的操作，所以栈最深为N － 1。空间复杂度为o(N)。平均空间复杂度为o(log2N)。</p>
<h3>测试：</h3>
<p>100000的数据，基本无序，平均用时只要12 ms。跟前面的排序相比，简直效率提升了不止100倍。</p>
<p>语言Javascript。</p>
