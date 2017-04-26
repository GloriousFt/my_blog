---
layout: post
title: "前端方向的一些零散小问题"
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

### 1. HTTP与HTTPS的区别

* HTTP是超文本传输协议,信息是明文传输,HTTPS则是具有安全性的SSL加密传输协议.
* HTTP和HTTPS使用的是完全不同的连接方式,用的端口也不一样,前者是80,后者是443.
* HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份.

### 2. HTTP的POST与GET具体区别

显而易见,POST是发送数据,GET是请求数据,但是其实两者都可以发送数据与接收数据.

* POST请求不会被缓存,GET会被缓存.
* POST请求如果带数据是不会显示到URL上的,而GET会显示在URL上,GET的安全性较差,不可用于身份认证登陆.
* POST对数据长度无限制,而GET有限制.

### 3. Javascript与传统面向对象语言有何不同

Javascript是一种原型语言,类的概念在javascript中是过时的.虽然它可以模拟类这种设计方式,包括实现继承,多态等,但是它本身的设计方式是组合方式.

### 4. Javascript函数方法call和apply区别

它们都是调用一个对象的一个方法,但参数不同,call是传指定参数,而apply是传参数数组.

<!--more-->

### 5. CSS3一些新特性

边框border,阴影box-shadow,动画rotate,盒模型box,文字效果word-wrap,透明度opacity等.

### 6. Ajax原理

异步js与xml,不需要重载html,只对某部分进行异步更新.

```javascript
var xmlhttp = new XMLHttpRequest();
xmlhttp.open(method, url, true);
xmlhttp.send();
```

### 7. 一些前端优化方法

* 使用CDN，减小服务器负担
* 减少HTTP请求数,合理设置HTTP缓存
* 样式引用放到<head>中,先下载好CSS可以先渲染页面
* 减少DOM访问与操作次数
* 减少DOM节点数量
* 尽量使用外部资源CSS,Javscript,可以被浏览器缓存
* 避免重复请求资源
* 使用<link>代替@import,页面加载时,link的内容也会同时加载进来,但是@import是在页面加载后才把CSS加进来,相当于将css置底
* 资源压缩

### 8. Javascript数据类型

Number, Boolean, String, Array, Object, NULL, Undefined...

### 9. 浏览器访问一个网站的过程

* 浏览器先要解析域名,查找浏览器缓存,如果没找到则会查找本地hosts文件.若还没找到则会访问DNS服务器,利用迭代或是递归方式来进行域名IP查找,最终拿到域名对应的IP.
* 有了IP,浏览器利用HTTP会与IP对应的服务器进行通信,HTTP采用TCP的连接方式,会有"3次握手"建立链接.
* 建立好链接则可以用HTTP进行数据传输,获得到服务器端监听80端口返回的HTML文件资源.
* HTML中会有各种各样的资源,浏览器会解析HTML来进行每一种资源的获取.
* 有些资源是异步获取的,所以浏览器会先进行渲染,展示页面,最终形成一个用户所见的网页.

### 10. Javascript中instanceof和typeof的区别

typeof是用来返回变量类型的,不区分Array和Object,而instanceof是用来判断某变量是不是某对象实例的.

### 11. Session和Cookie的区别

* 它们都是记录用户状态的机制,但Cookie是存在浏览器端,而Session是对服务器端而言的.
* Cookie相对来说不安全,可在客户端主机上直接获取得到.
* Session是通常存在服务器端的内存中的.
* Session没被访问一次都会更新上次访问时间.
* Session的ID通常需要Cookie存储,或从URL中获取.

### 12. HTML常见行内元素

img, input, span, label, select, a, textarea...

### 13. DOCTYPE的含义

DOCTYPE声明了文档类型.HTML5只有一种文档类型,HTML4.01有三种.
`!DOCTYPE HTML`就是声明了浏览器要用HTML5的标准解析来渲染页面.

### 14. CSS垂直居中

* 父元素display设置为`table-cell`.

```css
.parent {
    vertical-align: middle;
    display: table-cell;
}
```

* 若父元素高度固定,则可计算高度,用子元素`margin-top`来实现.
* 相对定位,`top:50%`.

### 15. HTML5语义化标签

header, footer, aside, section, article, hgroup, nav...

### 16. 浏览器缓存和Cookie的关系

Cookie也是浏览器缓存的一种方式,不过多是用于用户认证的,通常设置数据缓存都是设置HTTP头部的cache-control(设置相对时间)或者expire(设置固定期限).
LocaleStorage,SessionStorage的数据容量都比较大,cookie只有4kb,相对较小.

### 17. 前端跨域访问方法

* JSONP,JQuery中AJAX请求可以用JSONP（JSON+PADDING）。
  JSONP原理是站中生成一个script标签，src为地址，同时向服务器提供一个回调（可以写到URL中或是约定好），服务器用这个回调的名字作为函数包住JSON数据做返回，回调的参数就是返回的对象数据。然后js在回调中做后续处理。
* 代理服务器的方法,也就是将请求转到后台去,安全.
* HTML5中webSocket的方法.
* iframe的方法.
* window.postMessage(data,'url'); //url为另一站点

### 18. Javascript中的原型链

原型链类似于继承关系.

只有构造函数才有prototype,对象没有.

每个对象都有__proto__来记录自己的原型链,__proto__就是指向父的原型prototype.

### 19. Javascript中offsetHeight,clientHeight和scrollHeight的区别

* scrollHeight为整个页面内容的高度,当有滚动条时,它的值为当前可见窗口高度+滚动上去的高度+下面未滚动到的高度.
* clientHeight为当前可见区域内容的高度.
* offsetHeight为当前可见区域内容高度+边框+滚动条的高度.

**注意:上述可见内容高度都算上了padding.**

### 20. Javascript闭包的意义与作用

闭包的形式就是一个函数定义立即执行并返回了一个内部函数,这个内部函数可以操作其内部变量.

作用:最大的作用就是可以用闭包实现一个函数内部变量的私有化.如下例,`private_counter`和`increase`无法直接被调用,闭包起到了私有化的作用.

缺点:容易造成内存极大消耗.

例子:

```javascript
var Counter = (function() {
    var private_counter = 0;
    function increase() {
        private_counter++;
    }

    return {
        plus1 : function() {
            increase();
        },
        value : function() {
            return private_counter;
        }
    }
})();

var counter1 = new Counter();
counter1.plus1();
counter1.value();
```

### 21. 正向代理与反向代理

* 正向代理的代理对象是客户端,客户端知道服务端的情况,由代理服务器将内容取回来发给客户端.
* 反向代理的代理对象是服务端,客户端不知道最终服务端的情况.

### 22. Javascript中"=="与"==="的对比

"=="只要两边值相同就返回true,有一个类型转换的过程,"==="两边的值不仅要相同,类型也要相同.

例如:

```javascript
'1' == 1; //true
'1' === 1; //false
'1' === '1'; //true
```

### 23. HTML5新特性

* 多媒体标签,可以不再依赖flash.
* 一些新增API,如classList.
* 语义化标签,如aside,nav,header,footer等.
* webSocket,可以让客户端与服务器建立全双工通信.

### 24. 浏览器渲染过程

* 获取到HTML,构建DOM树.
* 根据head中CSS构建render树.
* 构建布局树,包含元素位置信息.

渲染与资源获取的过程是并行的.

### 25. 浏览器的repaint(重绘)与reflow(回流)

repaint指元素样式发生变化,但是位置,大小等信息没变,则只需要repaint这一区域的该元素即可.

reflow指的是位置或大小等影响布局的信息变化了,则需要将影响到的所有元素reflow.

### 26. 同源的概念

js不能访问直接非同源的资源,同源是指域名,协议,端口都相同.

### 27. Javascript事件委托

事件委托指的是不直接在元素上绑定监听事件,而是在其父元素上绑定,这样可以在一些情况下提高效率.

如:
```html
<ul>
    <li>...</li>
    ...
</ul>
```
绑定在ul上性能更好,减少事件绑定的元素.

### 28. Web安全问题

XSS攻击,跨站脚本攻击,恶意js代码注入.
场景:在网站A注册了一个带有恶意JS代码的用户，恶意代码可能为将document.cookie发送给攻击者网站，服务器将其存到了数据库中。这样当其他用户访问数据库获取到这个恶意代码并在页面上显示的时候，大量用户的cookie信息都会发送给攻击者。
防御方法:转义script,或后台进行转义防范.服务器端还可以进行HttpOnly设置,使浏览器禁止访问cookie.

CSRF攻击,跨站请求伪造.
场景:在网站A登录后产生了Cookie,在会话期有效时间内访问网站B,网站B伪造了一个访问A的链接,当用户在B上点击链接后,服务器接收到的请求无法区分A,B站来源,因为都有之前产生的cookie.所以服务器会再次接受请求并执行相应逻辑.
防御方法:正确使用POST和GET,还可以用验证码的方式拒绝其它站点的伪造请求.或者在提交表单时添加伪随机数,服务器端进行伪随机数校验.

### 29. HTML中的img标签有Alt和Title属性,有什么区别

* Alt是当图片资源无法读取时,用Alt属性值进行替代的方法.
* Title是图片的信息属性,当用户将鼠标放到图片时会显示title属性值.

### 30. XHTML和HTML的不同

XHTML其实就是HTML4.01,但是更为严格,以XML的标准,要求标签必须闭合,有错一定会在浏览器显示出来.
因为这一点导致极大多数的网页都不会用XHTML标准.

### 31. CSS清除浮动

```css
.clearfix:before,
.clearfix:after {
    content: "";
    line-height: 0;
    display: table;
}

.clearfix:after {
    clear: both;
}

```

### 32. HTTP头部的Content-Type是什么意思

Content-Type是用来规定数据类型的,可以是`application/x-www-form-urlencoded`,那携带的数据就为Form Data方式发送的.
如果是`text/plain;charset=UTF-8`,那么携带的数据就是以requestPayload的方式发送的.

### 33. Javascript中的继承

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.bark = function(sound) {
  console.log(sound);
}

function extend(Child, Parent) {
  Child.prototype = Object.create(Parent.prototype);
  Child.prototype.contructor = Child;
}

function Duck(name, age) {
  this.age = age;
}

extend(Duck, Animal);

var duck1 = new Duck('Donald', 2);
duck1.bark();
```

### 34. JQuery中的$(window).load和$(document).ready区别

load是所有内容都加载完后执行,包括图片等资源.
ready是DOM加载完时执行,此时图片等资源可能没完成加载.

### 35. Javascript中绑定事件的两种方式,事件冒泡和事件捕获

W3C默认是事件冒泡,addEventListener('click',function(){},false);
第三个参数为true则为事件捕获方式.

### 36. 多个页面间如何通信

通过Cookie,localeStorage,SessionStorage

### 37. Javascript中New关键字的实际过程

* 创建一个新的空变量,然后this指向它.
* 原型继承,空变量的__proto__指向New后面函数的prototype.
* 改变this指向,XX.call(空变量这个对象).

### 38. CSS属性box-sizing是做什么用的

*`box-sizing:content-box`,规定高度与宽度是盒模型的content高度,也是CSS3默认的.
*`box-sizing:border-box`,规定高度与宽度是盒模型的content与padding和border之和.

### 39. CSS3画三角和圆,椭圆

三角:
```css
.triangle {
    width: 0;
    height: 0,
    border-bottom: 5px solid #fff;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
}
```
圆,椭圆:
```css
.circle {
    width: 100px;
    height: 100px;
    //height: 50px; //椭圆
    border-radium: 50%;
    background-color: #000;
}
```

### 40.HTTP头部中expires和cache-control的作用

只有get请求才会被缓存,post不会.

cache-control中有不同的值,不同值的含义不同:
* no-cache表示不设置缓存.
* max-age表示客户端可以接收生存期不大于指定时间（以秒为单位）的响应.
* Public指响应可被任何缓存区缓存,Private指只能被当前用户缓存.

Expires 表示存在时间,允许客户端在这个时间之前不去检查（发请求）,等同max-age的效果.
但是如果同时存在,则被Cache-Control的max-age覆盖.

### 41.Cache-control的具体控制功能

* 1.打开新窗口
private,no-cache和must-revalidate,打开新窗口都会重新访问服务器.
max-age的话,有效期内不会重新访问.
* 2.地址栏回车
private,must-revalidate第一次会重新访问服务器.
no-cache每次都会重新访问.
max-age,有效期内不会重新访问.
* 3.后退
值为no-cache的话每次都会重新访问服务器.其他值不会.
* 4.刷新页面
无论何值,都会重新访问.

### 42.ETag和Last-Modified的作用

用作判断是否需要重新传数据的一个判断标志.
可以在服务器端进行配置.ETAG是HTTP1.1的,而Last-Modified是HTTP1.0的.

客户端第一次访问服务器时:
返回的HTTP头部会有ETAG和Last-Modified值.
客户端第二次访问服务器时:
请求的HTTP头部会有If-None-Match,值为ETAG值.还有If-Modified-Since,值为Last-Modified值.
服务器端接收到第二次请求时:
发现If-None-Match的值跟之前的ETAG值相同,则返回304,告知客户端Not Modified,客户端则可以继续用之前获得到的数据.

ETAG和Last-Modified相比具有更高优先级,后者只能到秒级,因此如在1s内连续访问服务器的话则需要进行ETAG的设置来进行优化.
另外如果服务器只对某个文件时间进行了修改,后者也会认为是修改了文件,因此还会重传文件,前者则不会.

### 43.Cookie Free优化原理
Cookie Free就是指将图片,css,js等静态资源从主域名的服务器上分离出来放到子域下的服务器上,通过设置cookie的domain使得请求这些资源时不再发送cookie.
同时,使用多个域名也可以加大数据请求并发量.

### 44.浏览器几种缓存情况
Last-Modofied,ETAG,Expires,Cache-Control

Last-Modofied,ETAG仍然需要浏览器访问一次服务器,如果服务器计算的ETAG相同,则返回304,无实体信息.

Expires,Cache-Control则不需要再访问服务器,只需要看有效时间即可.浏览器直接返回200(from cache).

上述的几种在刷新页面的情况下都会重新访问服务器.

### 45.HTTP2.0相比于HTTP1.1的重大改进


### TO BE CONTINUED