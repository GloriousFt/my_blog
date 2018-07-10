---
layout: post
title: "Local Storage的介绍与使用说明"
date: 2018-07-10 20:03:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- HTML5
- local storage
- indexedDB
meta:
  _edit_last: '1'
---

前几天看了HTML5的由来，系统性地看了HTML5的各个特性，虽然有些已经很常用了，但还是记录下来留着以后翻看。这次就先记录local storage的使用方法。

### 1.什么是Local Storage

Local Storage就是一种浏览器客户端数据存储的方法，它能够永久地存储一些web应用数据，即使用户已经离开了该Web应用。

虽然听起来有些像cookie的作用，但其实它的设计初衷跟cookie是不同的，它是为了解决“存储数据”这个问题的，而cookie是为了进行通信校验的，具体不同体现为：

* 客户端每次发送请求都会携带cookie，cookie中会有一些作身份校验的信息如token等。即便是请求同源资源如图片等静态资源，客户端也会发送cookie。
* cookie容量很小，仅为4KB。而local storage容量为5MB。
* 浏览器对cookie的操作支持比较有限，只能通过`document.cookie`方法来获取并进行字符串操作。而local storage则有较多的浏览器方法支持。

### 2.如何使用Local Storage

现代浏览器提供了四个基本方法来操作local storage：

| 名称        |  参数     |  说明   | 
| --------   |  -------  | ------ |
| getItem     | key{String}  | 获取存储数据 | 
| setItem     | key{String},value{Object}  | 设置存储数据 |
| removeItem  | key{String}  | 删除存储数据 |
| clear       |   | 清空所有数据 |

<!--more-->

local storage是根据键值对的方式存储数据的，getItem是根据Key值来获取数据，但取出来的数据都是字符串类型，需要开发者自己格式化数据。

setItem可以设置存储任意数据类型。

removeItem是指定key来删除数据。

clear则删除所有该域名下的local storage数据。

如果想要遍历local storage，可以通过遍历它的key来获取数据。

```javascript
for (var i = 0; i < window.localStorage.length; i++) {
  console.log(window.localStorage.key(i));
}
```

还可以设置storage的监听事件。

```javascript
window.addEventListener('storage', function(e) {
  console.log(e.key);	
  console.log(e.oldValue);	
  console.log(e.newValue);	
  console.log(e.url);	
});
```

需要注意的是，这个监听事件只能起到只读的效果，也就是说开发者无法阻止事件发生，也无法改变event参数的值。

### 3.如何存储更大容量的数据-IndexedDB

IndexedDB是基于JS的面向对象的事物型数据库系统，允许开发者存储和检索用键索引的对象。

IndexedDB 鼓励使用的基本模式如下：

* 打开数据库并且开始一个事务。
* 创建一个 object store。
* 构建一个请求来执行一些数据库操作，像增加或提取数据等。
* 通过监听正确类型的 DOM 事件以等待操作完成。
* 在操作结果上进行一些操作（可以在 request 对象中找到）。

更多的API使用方法可以参考[Indexed Database](http://w3c.github.io/IndexedDB/#introduction)。
