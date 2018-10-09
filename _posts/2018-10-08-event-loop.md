---
layout: post
title: "Event Loop中的task和microtask"
date: 2018-09-30 10:55:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- microtask
- eventloop
meta:
  _edit_last: '1'
---

## 1.前言

最近做一个移动端的项目优化，发现vue的nextTick用到了mutation observer方法，后来继续研究下去，发现mutation observer的回调都是放进microtask队列的，而UI的render是在每一次事件循环后就执行的。

本文将着重对事件循环-event loop的概念做讲解，如果你对event loop已经有一定了解，不妨直接看第四部分。

## 2.Event Loop是什么

Javascript引擎是单线程的，这就意味着js脚本只能是单线程的，因此要有一种机制让js引擎知道哪些任务要先执行，哪些可以放到后面执行。这个机制就是事件循环-Event Loop。

Event Loop有三个部分，task队列，microtask队列和js执行栈。

### 2.1 Task
Task队列是用来存放浏览器要执行的任务的，根据[HTML标准](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)，task主要包括：

* Event
分派事件
* Parsing
HTML解析器，解析字节并处理解析结果。
* Callbacks
一些异步事件回调 
* Using a resource
* Reacting to DOM manipulation
一些异步DOM操作的反馈，如在HTML文档中插入节点。

因此，如setTimeout方法的回调也是一个task。

### 2.2 Microtask
Microtask队列通常保存那些在当前js脚本执行完后需要立即执行的任务，当js执行栈为空时，如果当前执行的task是callback，即便task任务执行到一半也会按序执行microtask队列的任务，将其队列内所有的microtasks执行完。同时，microtask也会在每一个task
执行完后执行。

Microtask队列包括Promise回调，Mutation Observer回调。

### 2.3 JS Stack
JS执行栈初始化的时候为空，其余时间将会一直有任务运行。当执行栈运行完任务为空后，就会从microtask取任务继续执行，直到microtask队列为空，然后继续从task队列取出一个任务执行。

<!--more-->
## 3.Event Loop处理过程

每个浏览器对Event Loop的处理并不相同，下面的只是HTML的标准，但每个浏览器的处理具体并不完全相同。

* JS执行栈初始化，执行JS脚本。
* 从task队列按顺序取出一个task。
* JS执行栈运行这个task，如果task是callback，那么当执行栈为空时microtask也会立即执行。
* 从task队列移除上述执行的task。
* 按序执行microtask队列中的所有任务，如果在执行中有新的microtask添加进来，那么继续执行直到队列为空。
* 更新浏览器渲染。
* 回到第二步，继续重复整个过程。

## 4.具体代码分析

在66版本的Chrome浏览器环境下，下面这段代码，在点击inner的div后，控制台会log什么呢？
```html
<div class="outer">
  <div class="inner"></div>
</div>
```
```javascript
var outer = document.querySelector('.outer');
var inner = document.querySelector('.inner');

new MutationObserver(function() {
  console.log('mutate');
}).observe(outer, {
  attributes: true
});

function onClick() {
  console.log('click');

  setTimeout(function() {
    console.log('timeout');
  }, 0);

  Promise.resolve().then(function() {
    console.log('promise');
  });

  outer.setAttribute('data-random', Math.random());
}

inner.addEventListener('click', onClick);
outer.addEventListener('click', onClick);
```

在chrome下，控制台会打印：

click
promise
mutate
click
promise
mutate
timeout
timeout

我们一步一步来分析。

1. 首先执行栈为空，当用户点击了类为inner的div之后，onClick回调就会进入task队列。

2. 因为当前执行栈为空，microtask也为空，所以会从task取出一个任务（并不移除），这个任务就是event dispatched。

3. onClick方法执行，所以先打印出来`click`，接下来把setTimeout的回调放如task队列，此时task队列有两个任务了（event dispatched和setTimeout）。

4. 再接着把promise回调放到microtask队列里，此时microtask队列只有这一个任务。

5. 接着修改outer属性，将mutation observer回调放到microtask队列，此时microtask队列有两个任务。

6. onClick执行完，执行栈为空，而microtask队列不为空（此时task队列的event dispatched事件并未执行完，因为还有冒泡），并且执行的OnClick的task是callback，所以即使是在task任务执行过程中，JS引擎也会按序取出microtask
队列每一个任务执行，所以先打印`promise`，再打印`mutate`。

7. 执行完microtask队列任务，执行栈和microtask队列都为空，所以继续执行task队列的event dispatched事件。

8. 因为冒泡的原因，所以onClick事件会再执行一次。

9. 当event dispatched执行完，task队列会将这个任务删掉，再取一个新的任务，此时task队列只剩两个setTimeout回调。

10. 分别执行setTimeout回调，打印`setTimeout`。

当你用别的浏览器测试这段代码时，log可能会不太一样，因为有的浏览器会把promise也当作task处理而不是microtask。

如果把触发点击的事件改为`inner.click()`而不是由用户交互产生呢？

我们在上述代码的最后加上：

```javascript
inner.addEventListener('click', onClick);
outer.addEventListener('click', onClick);

// 新加代码
inner.click();
console.log('main');
```

Chrome下控制台打印结果为:

click
click
main
promise
mutate
promise
timeout
timeout

不妨先想一下为什么会这样，与上述代码有何不同。

1. 首先，第一个例子的代码在初始化时，task队列是为空的，能执行到onClick是因为事件监听。但后面这个代码不一样，这个是执行到`inner.click()`才触发的事件，因此，task队列此时其实是有任务的，任务内容就是这一段代码的执行。因此Task队列内容为Run script。

2. inner同步调用onClick，所以先打印`click`，然后setTimeout进入task队列，此时task队列有两个任务，一个是当前的Run script，一个是新加的setTimeout回调。

3. 继续运行onClick，将Promise回调放入microtask队列，再将mutation observer回调放入microtask队列。

4. 此时运行栈并不为空，因为还有同步的代码没执行完，也就是冒泡的onClick，所以会重复一遍onClick执行。

5. 在第二次运行的onClick方法里，需要注意的是setAttribute的DOM操作，这是一个同步的DOM操作，所以会立即修改属性。修改后会触发mutation observer的回调，但是根据[DOM标准](https://dom.spec.whatwg.org/#queue-a-mutation-observer-compound-microtask)规定当microtask队列中有一个mutation 
observer任务时，则不再向队列里添加mutation observer类型的任务（即便是不同的DOM操作）。所以第二个mutation 
observer则不会添加到microtask队列中。但是这并不意味着DOM操作没执行，只是执行了但是没把回调放入microtask队列而已，此时microtask队列内有三个任务，类型分别为promise，mutation observer和promise。

5. onClick执行完，执行栈还不为空，因为后面还有个console.log('main')未执行，所以microtask队列还是无法执行，要先打印`main`。

6. 所有同步代码执行完，将当前的task-Run script移出task队列，执行栈为空，而microtask队列不为空，所以依次打印`promise`, `mutate`, `promise`。

7. microtask队列执行完为空，继续从task队列取出任务依次执行，所以打印两个`setTimeout`。

Event Loop就先介绍这么多，有问题可以留言讨论。


