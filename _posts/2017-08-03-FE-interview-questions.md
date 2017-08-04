---
layout: post
title: "前端常见问题（二）"
date: 2017-08-03 10:29:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript, Closure, Prototype, CSRF, XSS
meta:
  _edit_last: '1'
---

### 1.Javascript 闭包
闭包通常指，在一个外围函数内，通过返回一个函数这种形式来操作外围函数内的变量，即使当外围函数执行过后也不会释放其内部被闭包的变量。

示例：
```javascript
function Counter() {
    var count = 0;
    function inc() {
        count++;
    }
    return {
        getCount : function() {
            return count;
        },
        increase : function() {
            inc();
        }
    }
}
var counter1 = new Counter();
var counter2 = new Counter();
console.log(counter1.getCount()); // 0
counter1.increase();
console.log(counter1.getCount()); // 1
console.log(counter2.getCount()); // 0
```

用闭包实现私有变量。

<!--more-->

### 2.Javascript 原型继承
Javascript中的对象都会有一个私有属性`__proto__`，它指向这个对象的原型对象的`prototype`。
同时，原型对象也会同样的有它自己的原型，这样下去就形成了一个原型链。

当要访问一个对象的属性时，不仅仅会在它自己的属性上查找，还会到它的原型链上去查找。

示例：
```javascript
var a = {
    foo : "1",
    bar : "2"
}
```
定义好之后可以发现，`a`的私有属性`__proto__`指向了`Object`，这是因为`a`是一个对象，它继承了Object的方法和对象。
因此，它的`__proto__`就是`Object`的`prototype`。

在Javascript中，只要是函数就会有`prototype`属性。

利用以上的原型链就可以实现一个Javascript中的继承方法：
```javascript
function extends(Child, Parent) {
    Child.prototype = Object.create(Parent.prototype);
    Child.prototype.constructor = Child;
}
```
示例：
```javascript
function extend(Child, Parent) {
    Child.prototype = Object.create(Parent.prototype);
    Child.prototype.constructor = Child;
}
function Duck() {
  this.name = "Child";
}

function Animal() {}
Parent.prototype.bark = function(sound) {
    this.sound = sound;
    console.log(sound);
};

extend(Duck, Animal);

var child = new Child();

console.log(child.sound);
child.bark('Ga!');
console.log(child.sound);
```
`new`的过程发生了什么？
```javascript
var a = new A();
```
其实是
```javascript
var a = Object.create();
a.__proto__ = A.prototype;
A.call(a);
```

### 3.前端性能优化
优化可以从以下两个方面进行：
* 1.页面优化
* 2.代码优化

**页面优化**主要有：

#### 1.减少HTTP请求数

减少HTTP请求可以从很多方面进行。

* 优化页面结构，简化页面数据，一个页面数据请求尽可能少。
* 对资源进行合理缓存设置。
HTTP/1.0和HTTP/1.1中可以分别在服务器返回的响应头中加带字段Last-modified和ETag字段。
客户端再次请求时会在请求头中分别发送If-Modified-Since和If-None-Match字段，服务器就可以根据这些字段判断资源是否需要重新发送，如果不需要则返回304状态码，表示Not Modified。
* 资源的合并与压缩。
现在有很多项目工程化工具可以对js文件和css文件进行合并与压缩。
* 部分图片资源可以用css sprite。

#### 2.将css放到head中加载。
这也符合页面生成顺序，先生成DOM，再生成CSSOM，然后根据这两个生成渲染树。先加载css可以让页面先知道要渲染的样式。
放到后面加载可能会造成reflow和repaint。

#### 3.将js脚本文件放在最后面。
js脚本会阻塞页面读取资源，在下载读取当前脚本文件时浏览器会等待脚本下载。因此放在后面可以先让用户看到页面。
当然可以用`defer`和`async`来控制脚本加载方式。

#### 4.使用外部js和css资源。
方便浏览器进行缓存。

**代码优化**有：

#### 1.绝对的减少DOM操作。
DOM操作会带来大量性能消耗，浏览器会遍历DOM进行搜索。

#### 2.在用js写style时，要注意尽可能的减少reflow和repaint。

#### 3.用js时要注意减少原型链查找。
尽量用`hasOwnProperty`方法。

#### 4.css选择器减少子选择器方式。
如果只是找其下面一层，可以用`>`替代子选择器。

### 4.前端安全性问题 - CSRF与XSS
前端最常见的两个安全性问题CSRF与XSS。

XSS是跨站脚本攻击，基本原理就是通过在页面上注入js代码对页面进行破坏或者进行数据窃取。
注入的方式基本都是在用户输入处进行js代码输入。

**防御方式**：对用户输入内容进行严格控制，对输入内容转义。

CSRF是跨站请求伪造，基本原理是，在某一第三方网站构造假链接，链接内容可能是用户最近时间在浏览的某一网站的数据接口或者其他链接。
当用户点击时则会触发这一链接，通知服务器进行相应数据操作，因为服务器的session可能还没过期，因此接受了http请求的cookie信息，受理了这一请求，对服务器数据造成破坏。

**防御方式**：
* 大型应用可以用验证码这一方式，验证码的内容通常是无法伪造的。
* 还可以用token的方式，前端在向后台发请求时要带着一个从服务器建立session时发给用户的token，这个token服务器端就存储在session中。
当前端发送请求时要带着这个token，后台校验token成功后再进行后续操作。
* 当然最基本的还是要明确GET，POST，PUT等请求，不能随便用GET来更新后台数据库。

### 5.HTTP协议相关

#### HTTP Cookie：
Cookie可以在HTTP响应报文中通过`Set-Cookie`字段进行设置。
在以后的每次通讯中cookie都会被携带发送以便识别用户身份。

会话期cookie会在浏览器关闭后自动删除。

永久性cookie可以通过HTTP头部`Set-Cookie`设置来设置有效期。

`HttpOnly`可以让前端无法通过`Document.cookie`来获取cookie。

#### HTTP缓存
缓存只会缓存GET请求的数据。

HTTP通过Cache-Control和Expires头部字段来设置资源缓存的有效期。
当同时存在时，先以Cache-Control的Max-Age为主。

加速缓存是说，对于资源来说缓存期限都应该设置的尽可能长才好，但是当一些资源（js或css）急需更新时，服务器更新了资源后，客户端可能还是在缓存进行数据获取。
这时就可以用更改资源名的方式进行优化。在资源名后面加上一个"版本号"，这样缓存也会更新这个新的文件。当请求时，也要在相应的url上加上"版本号"。

好处就是可以及时更新资源。

缺点就是要在资源引用处进行相应更改。

#### HTTP状态码：
* 1XX表示信息回应。
* 2XX表示成功。
* 3XX表示请求重定向。
    * 301 资源重定位，资源URI被更改了。
    * 304 未修改，缓存回应。
* 4XX表示客户端请求错误。
    * 400 错误请求
    * 401 未授权
    * 403 被禁止
* 5XX表示服务器内错误。
    * 500 服务器内部错误

### 6.HTTP与HTTPS协议

HTTPS是用SSL协议加密了明文HTTP的协议，更加安全。

**加密原理**：

* 1.客户端发送一个HTTPS请求，服务器端接收后把申请的证书的公钥发给客户端。
* 2.客户端接收后，用这个公钥对一个随机生成的另一个公钥进行加密，发给服务端。
* 3.服务端用之前的私钥解密获得客户端发过来的公钥，然后用这个公钥把要返回的数据加密返回。
* 4.客户端收到报文后，用自己的私钥来解密数据，就得到了最终的数据。

### 7.React生命周期
第一次实例化时：
* getDefaultProps
* getInitialState
* componentWillMount
* render
* componentDidMount

实例更新：
* componentWillReceiveProps
* shouldComponentUpdate
* componentWillUpdate
* render
* componentDidUpdate

析构：
* componentWillUnMount