---
layout: post
title: "前端方向的一些零散面试小问题"
date: 2017-02-27 14:34:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Interview
- Front-end
meta:
  _edit_last: '1'
---
以下是博主自己总结的一些前端知识问题:

## 1. HTTP与HTTPS的区别

* HTTP是超文本传输协议,信息是明文传输,HTTPS则是具有安全性的SSL加密传输协议.
* HTTP和HTTPS使用的是完全不同的连接方式,用的端口也不一样,前者是80,后者是443.
* HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份.

## 2. HTTP的POST与GET具体区别

显而易见,POST是发送数据,GET是请求数据,但是其实两者都可以发送数据与接收数据.

* POST请求不会被缓存,GET会被缓存.
* POST请求如果带数据是不会显示到URL上的,而GET会显示在URL上,GET的安全性较差,不可用于身份认证登陆.
* POST对数据长度无限制,而GET有限制.

## 3. Javascript与传统面向对象语言有何不同

Javscript是一种原型语言,类的概念在javascript中是过时的.虽然它可以模拟类这种设计方式,包括实现继承,多态等,但是它本身的设计方式是组合方式.

## 4. Javascript函数方法call和apply区别

它们都是调用一个对象的一个方法,但参数不同,call是传指定参数,而apply是传参数数组.

<!--more-->

## 5. CSS3一些新特性

边框border,阴影box-shadow,动画rotate,盒模型box,文字效果word-wrap,透明度opacity等.

## 6. Ajax原理

异步js与xml,不需要重载html,只对某部分进行异步更新.

{% highlight javascript %}
var xmlhttp = new XMLHttpRequest();
xmlhttp.open(method, url, true);
xmlhttp.send();
```
{% endhightlight %}

## 7. 一些前端优化方法

* 减少HTTP请求数,合理设置HTTP缓存
* 样式引用放到<head>中,先下载好CSS可以先渲染页面
* 减少DOM访问与操作次数
* 减少DOM节点数量
* 尽量使用外部资源CSS,Javscript,可以被浏览器缓存
* 避免重复请求资源
* 使用<link>代替@import,页面加载时,link的内容也会同时加载进来,但是@import是在页面加载后才把CSS加进来,相当于将css置底
* 资源压缩

## 8. Javascript基本数据类型

Number, Boolean, String, Array, Object, NULL, Undefined

## 9. 浏览器访问一个网站的过程

* 浏览器先要解析域名,查找浏览器缓存,如果没找到则会查找本地hosts文件.若还没找到则会访问DNS服务器,利用迭代或是递归方式来进行域名IP查找,最终拿到域名对应的IP.
* 有了IP,浏览器利用HTTP会与IP对应的服务器进行通信,HTTP采用TCP的连接方式,会有"3次握手"建立链接.
* 建立好链接则可以用HTTP进行数据传输,获得到服务器端监听80端口返回的HTML文件资源.
* HTML中会有各种各样的资源,浏览器会解析HTML来进行每一种资源的获取.
* 有些资源是异步获取的,所以浏览器会先进行渲染,展示页面,最终形成一个用户所见的网页.

## 10. Javascript中instanceof和typeof的区别

typeof是用来判断变量类型的,而instanceof是用来判断某变量是不是某对象实例的.

## 11. Session和Cookie的区别

* Cookie是浏览器端的缓存,而Session是服务器端的缓存.
* Cookie相对来说不安全,可在客户端主机上直接获取得到.
* Cookie是浏览器端为了区分不同用户数据而形成的,弥补了HTTP的无状态性,而Session是服务器端为了记录不同用户访问状态的服务器端存储.

## 12. HTML常见行内元素

img, input, span, label, select, a, textarea...

## 13. DOCTYPE的含义

DOCTYPE声明了浏览器对该文档解析的方法,`!DOCTYPE HTML`就是声明了浏览器要用W3C的标准解析来渲染页面.

## 14. CSS垂直居中

* 父元素display设置为`table-cell`.

```css
.parent {
    vertical-align: middle;
    display: table-cell;
}
```

* 若父元素高度固定,则可计算高度,用子元素`margin-top`来实现.
* 相对定位,`top:50%`.

## 15. HTML5语义化标签

header, footer, aside, section, article, hgroup, nav...

## 16. 浏览器缓存和Cookie的关系

Cookie也是浏览器缓存的一种方式,不过多是用于用户认证的,通常设置数据缓存都是设置HTTP头部的cache-control(设置相对时间)或者expire(设置固定期限).

## 17. 前端跨域访问方法及跨域问题

* JSONP,JQuery中AJAX请求可以用JSONP格式数据,同时要修改服务器端.
* 转到后台去请求,安全.
* window.postMessage(data,'url'); //url为另一站点

XSS攻击,恶意js代码注入. 防御方法:转义<script>,或后台进行转义防范.

CSRF攻击,跨站请求伪造. 防御方法,正确使用POST和GET,还可以用验证码的方式拒绝其它站点的伪造请求.

## 18. Javascript中的原型链

原型链类似于继承关系.

只有构造函数才有prototype,对象没有.

每个对象都有__proto__来记录自己的原型链,__proto__就是指向父的原型prototype.

## 19. Javascript中offsetHeight,clientHeight和scrollHeight的区别

* scrollHeight为整个页面内容的高度,当有滚动条时,它的值为当前可见窗口高度+滚动上去的高度+下面未滚动到的高度.
* clientHeight为当前可见区域内容的高度.
* offsetHeight为当前可见区域内容高度+边框+滚动条的高度.

**注意:上述可见内容高度都算上了padding.**

## 2.TO BE CONTINUED