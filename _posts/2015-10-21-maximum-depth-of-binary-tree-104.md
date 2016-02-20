---
layout: post
title: Maximum Depth of Binary Tree(104) 递归做法
date: 2015-10-21 13:13:39.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Algorithm
- LeetCode
meta:
  _edit_last: '1'
---
<p>要求：<br />
二叉树的最大深度</p>
<p>二叉树和递归，我不太扎实的地方。今天就来简单的自己分析一下。</p>
<p>二叉树的数据结构很简单<br />
<code><br />
public class TreeNode {<br />
      int val;<br />
      TreeNode left;<br />
      TreeNode right;<br />
      TreeNode(int x) { val = x; }<br />
}<br />
</code></p>
<!--more-->
<p>重点是递归，递归一定要有出口，不然会死循环，还要有递归条件。比如这个题，求二叉树的最大深度，可以用递归来解决。<br />
给定任意节点，先算他的左右节点高度，如果左高则左高度加1，右高则右高度加1，然后判断左右高度，返回高的值。这样这个节点的高度值就有了，如果它没有左右子树，则返回1。这样递归下去，一直到所有节点都遍历到。</p>
<p>Code：<br />
<code><br />
public class Solution {<br />
    public int maxDepth(TreeNode root) {<br />
        int leftDepth, rightDepth;<br />
        if(root == null) {<br />
            return 0;<br />
        } else {<br />
            if (root.left != null) {<br />
                leftDepth = maxDepth(root.left);<br />
            } else {<br />
                leftDepth = 0;<br />
            }<br />
            if (root.right != null) {<br />
                rightDepth = maxDepth(root.right);<br />
            } else {<br />
                rightDepth = 0;<br />
            }</p>
<p>            return leftDepth > rightDepth ? leftDepth + 1 : rightDepth + 1;<br />
        }<br />
    }<br />
}<br />
</code></p>
