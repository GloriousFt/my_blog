---
layout: post
title: "React的虚拟DOM优势在哪里"
date: 2017-08-04 15:03:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript
- React
- Virtual DOM
meta:
  _edit_last: '1'
---

最近学习了React，一直在想React的优势在哪里。都说虚拟DOM提升了性能，可真的是这样吗？

先来简单说下React的几个概念：

#### 1.Component
React通过component来构建整个页面，每个大的component可以由很多小的component组合构成。每个component有着自己的生命周期。
#### 2.State
State是每个component内部的动态数据，也是由开发者维护管理的页面数据。凡是页面需要动态显示的地方都会有state来负责数据存储。

再来想一下，什么时候state变化了，component才会带着新的state重新渲染页面呢。

就是`setState`的时候。

Virtual DOM其实就是在这时发挥作用的，它是用javascript写的一个拥有DOM层级关系的一个数据结构。可以想成一个简化的DOM。当state变化时，component会重新触发render，那么Virtual DOM也会变化。
Virtual DOM会根据Diff算法来计算出哪里有变化，然后把新的Virtual DOM转换为真实的DOM，触发浏览器的渲染。

那这时就有一个问题，如果我只是更改一个`<label>`标签的值，那我直接DOM操作是不是更快一点呢？

答案是肯定的，因为只修改一个值，React还要经过render，Diff算法，DOM操作。这显然要比直接DOM修改一个节点的值要慢。

<!--more-->

那么Virtual DOM的优势在哪里呢？

试想，在一个Component中，开发者要一次修改state中的很多属性，属性可能是对象，数组，每一个属性都会对应到页面中的一个节点的值。

那么在这个时候，如果我们直接用DOM操作就显得不那么方便与高效了，比如根据state要更改一个样式，还有要根据state更改页面结构等等。

不同的直接DOM操作会触发浏览器不同的repaint和reflow，这时，Virtual DOM的优势就来了。

在用`setState`顺利触发了component的render后，react会对Virtual DOM进行操作，而这些操作并不会触发浏览器的reflow和repaint，因为Virtual DOM它只是存在内存中的一个有着DOM层级关系的数据而已。
当最终形成了新的Virtual DOM后，转换成真实的DOM这一步才会触发浏览器的reflow和repaint。这是不是就突显出Virtual DOM的优势了。

它优化了触发浏览器reflow和repaint的步骤，把众多页面节点改动集中到一次来进行触发。

简单来说，**它减少了同一时间内的页面多处内容修改所触发的浏览器reflow和repaint的次数，可能把多个不同的DOM操作集中减少到了几次甚至一次，优化了触发浏览器reflow和repaint的次数。**。

这才是React真正高效的地方，而且高效也是分情况的，绝不是网上说的在某个Diff时间片内，把DOM操作收集起来，集中到1次，变更到Virtual DOM上。
当一个component只有一个地方要更改时，显然直接DOM操作要比React的这一套要有效率的多。

多看，多学，多思考。





