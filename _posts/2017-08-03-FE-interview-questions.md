---
layout: post
title: "前端常见问题（二）- 进阶篇"
date: 2017-08-03 10:29:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript
- Closure
- Prototype
- CSRF
- XSS
meta:
  _edit_last: '1'
---

### 1.Javascript 闭包
闭包通常指，有权访问另个函数作用域中的变量的函数.
形式为在一个外围函数内，通过返回一个函数这种形式来操作外围函数内的变量，即使当外围函数执行过后也不会释放其内部被闭包的变量。

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
Animal.prototype.bark = function(sound) {
    this.sound = sound;
    console.log(sound);
};

extend(Duck, Animal);

var child = new Duck();

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

1.优化页面结构，简化页面数据，一个页面数据请求尽可能少。

2.对资源进行合理缓存设置。
HTTP/1.0和HTTP/1.1中可以分别在服务器返回的响应头中加带字段Last-modified和ETag字段。
客户端再次请求时会在请求头中分别发送If-Modified-Since和If-None-Match字段，服务器就可以根据这些字段判断资源是否需要重新发送，如果不需要则返回304状态码，表示Not Modified。

3.资源的合并与压缩。
现在有很多项目工程化工具可以对js文件和css文件进行合并与压缩。

4.部分图片资源可以用css sprite。

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
* 还可以用token的方式，前端在向后台发请求时要带着一个从服务器建立session时发给用户的token，这个token服务器端就存储在session中。当前端发送请求时要带着这个token，后台校验token成功后再进行后续操作。
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

### 8.CORS跨域请求
CORS只能用不低于IE10以上的浏览器实现。

它是一种实现跨域请求的方法，浏览器对这种请求分为两种模式：简单模式，非简单模式。

简单请求满足的条件为：
* 1.请求类型为HEAD，GET或POST中的一种。
* 2.HTTP请求头信息只能最多包含：Accept，Accept-Language，Last-Event-Id，Content-Language，Content-Type。Content Type只能为application/x-www-form-urlencoded、multipart/form-data、text/plain。

简单请求原理是，客户端发送XMLHttpRequest，如果是跨域的，浏览器会加上字段origin。

如果服务器许可接收origin字段的域发来的请求，那么会在响应报头上多出四个字段：
* Access-Control-Allow-Origin，表示接收的域名请求，"*"表示接收任意域名请求。
* Access-Control-Allow-Credentials，只能为true，表示可以带cookie访问。
* Access-Control-Expose-Headers，表示返回的其他的自定义字段名。
* Content-Type

如果要带着cookie请求，浏览器还要设置`withCredential=true`。而且服务器返回的`Access-Control-Allow-Origin`就不能是"*"。

非简单请求要先发送一个预检的HTTP请求，请求类型为`OPTIONS`。
多包含了：
* Origin，表示请求来自的源。
* Access-Control-Request-Method，表示请求的方法。
* Access-Control-Request-Headers，表示加带的其他字段。

服务器接收到这个请求之后，判断Origin是否许可，然后返回HTTP：
* Access-Control-Allow-Methods，表明服务器支持的所有跨域请求的方法。
* Access-Control-Allow-Headers，表明服务器支持的所有头信息字段。
* Access-Control-Allow-Credentials，表明是否可以带cookie。
* Access-Control-Max-Age，表明这次检索的有效期。

### 9.浏览器内核

* 1.Trident IE
* 2.Gecko Firefox
* 3.Webkit Safari
* 4.Blink Chrome

### 10.浏览器缓存机制

浏览器第一次向服务器请求资源后，服务器返回报文会有Cache-Control，ETag，Last-Modify和Expires字段做相关缓存机制的实现。

当浏览器再次访问服务器时，会先根据Cache-Control的值进行后续动作。

如果Cache-Control为Max-Age，那么它表示缓存的时间，浏览器会先判断缓存时间是否过期，如果没过期则直接浏览器返回200（from cache），并把缓存数据返回。

Cache-Control有不同的值，分别表示不同的机制，当它的值不是Max-Age时，那么有效时间由字段Expires提供。

当有效时间过期时，那么会有服务器判断是否服务器这边对请求的资源有更改，如果没有更改则让浏览器继续使用之前的资源，也就是返回304（not modified）。

资源是否有更改的机制则是服务器根据之前发送的ETag和Last-Modify字段来进行判别的，HTTP／1.0是Last-Modify，HTTP／1.1是ETag。

### 11.Javascript的组成

Javascript由三部分组成：
* ECMAScript 语法核心
* DOM 文档对象模型
* BOM 浏览器对象模型

ECMAScript是Javascript的语法核心，它规定实现了JS的语法、类型、语句、关键字、对象等，提供核心语言功能。

DOM提供了对HTML操作交互的API。

BOM提供了对浏览器操作的API，如navigator对象，location对象，screen对象等。

### 12.defer和async的区别

defer是定义延迟脚本，表示立即下载，但延迟执行，HTML5规定执行顺序为从上到下按序执行。

async是定义异步脚本，表示立即下载，但执行顺序不确定，下载完成后执行。

### 13.函数中arguments

arguments表示函数中的实参，当arguments内容改变时，其对应函数的实参也会被改变，但是这个改变是同步改变，不是指向同一地址的改变。

arguments的操作跟数组相似，但它并不是一个数组，它不是Array的一个实例。

### 14.Javascript函数的参数传递

Javascript的参数都是值传递的，参数传递过程其实就是一个变量的复制过程。

参数传递时，会在函数局部作用域内创建一个局部变量，名字就为定义时的变量名，它的值就是在调用时传进来的变量的值。

传递规则为：

如果参数是基本数据类型，存放到栈上的话，那么调用时传递的参数就是栈内容的复制。

如果参数是引用数据类型，数据是存放到堆上的话，那么调用时传递的值其实是存放在栈上引用的地址的复制。

### 15.对象的转换方法

所有对象都有toString(), valueOf()和toLocaleString()方法。

valueOf()会返回对象的值。

toString()会返回对象的字符串表达值，如果是数组则返回以逗号分割的数组每一项拼接的字符串值。在数组中，它其实是调用了每一项的toString方法。

在Array.sort()的方法中，其实也是调用了每一项的toString才进行排序的。

toLocaleString()与toString()相同，不同的就是数组时调用的每一项的toLocaleString()。

### 16.数组的concat，slice，splice

concat方法会创建一个当前数组的副本，然后按参数拼接到新数组后面并返回副本。

slice方法会根据参数创建一个新数组。

splice方法会修改原数组，返回删除的项。

### 17.函数Function类型

函数名仅仅是指向函数的指针，因此：

```javascript
function f1(a) {
    alert(a);
}
var f2 = f1;
f2(1);
f1 = null;
f2(1);
```

会发现代码依然会执行alert，`f1 = null`只会把f1指针置空。

这也说明了为什么js没有函数重载，后定义的函数会覆盖前面的函数，因为函数名只是指针。

**函数声明**

函数声明会在执行环境中提前解析，这是解析器的特性，但用函数表达式的函数定义就不会提前解析，如：

```javascript
f1(2);
function f1(a) {
    alert(a);
}

f2(3);
var f2 = function(a) {
    alert(a);
}
```

结果会显示f1的alert，也就是2，而`f2(3)`这行代码则会报错，"f2不是一个函数"。

**函数的属性和方法**

所有函数都有两个属性，length和prototype。

length表示要接受的参数的个数。

prototype是保存函数实例方法的真正所在。比如toString等方法都是在prototype里面的。

所有函数都有两个方法，call和apply。

它们都是用于改变函数的执行环境的，不同点在于接收的参数不同。

ES5中还定义了bind方法，它返回一个函数的实例，参数是执行环境的变量对象。

```javascript
var obj = {
    color : "red"
}
function showColor() {
    alert(this.color)
}
var bindColor = showColor.bind(obj);
bindColor(); //red
showColor(); //undefined
```

### 18.函数的执行环境

**匿名函数的执行环境具有全局性!**

当函数被调用时,会创建一个执行环境,每个执行环境都有一个与之关联的变量对象,变量对象中有作用域链.

作用域链其实就是一个指向变量对象的指针列表.对函数而言,就会把活动对象作为变量对象.

作用域链保存的是变量对象地址,并且有优先级顺序.

如:
```javascript
function test(a, b) {
  return a + b;
}

test(5, 10);
```

test函数在调用时创建了它的执行环境,内部包含了一个作用域链,内容为:

---- | --------------
1    | 全局变量对象
---- | --------------
0    | test的变量对象

test的变量对象有arguments,a和b.

`arguments = [5, 10];`
`a = 5;`
`b = 10;`

### 19.模块模式 Module Pattern

模块模式就是利用闭包实现建立单例对象的一种模式.

增强模块模式(单例是特定类型)

```javascript
var singleton = function() {
  var privateVar = 10;

  var obj = new Custom();
  obj.publicInc = function() {
    privateVar++;
  }

  return obj;
}();

singleton.publicInc();
```

### 20.web模拟移动端滑动事件

有以下移动端触摸事件:

touchstart, touchmove, touchend, touchcancel

每个事件都有一下属性:

* touches: 当前跟踪的触摸操作Touch对象数组.
* targetTouches: 特定于目标事件的Touch对象数组.
* changeTouches: 自上次触摸以来发生了什么改变的Touch对象数组.

每个Touch对象的属性:

clientX, clientY, pageX, pageY, screenX, screenY, identifier, target.

模拟手势滑动:

可以用touchstart和touchend.

在touchstart中取起始坐标值,clientX,clientY.

在touchend中获取结束的坐标点,然后计算差值,并重置起始坐标.

### 21.HTML5中js的postMessage

*postMessage:*

用在与包含在内部的<iframe>元素或者当前页面弹出的窗口进行数据交互时使用.

```javascript
//发送方
window.postMessage('text message', 'https://www.xxx.com');

//接收方
window.onmessage = function(event) {
  if (event.origin === 'https://www.xxx.com') {
    ...
    console.log(event.data); // 'text message'
    //继续操作
  }
  event.source.postMessage('received!', 'poster address');
};
```

### 22.Web Sockets跨域方法

Web Sockets是使用Web Sockets协议的一种建立全双工通信的方法.它只在第一次建立连接请求时使用了HTTP协议,之后都是ws协议.

它不限制同源,因此可以跨域,因为不使用HTTP协议,所以服务器也要做相应的变化.

```javascript
var socket = new WebSocket('www.xxx.com/server.php');
socket.open();
socket.send('text');
socket.onmessage = function (res) {
  alert(res.data);
}
socket.close();
```

### 23.indexedDB-浏览器数据库

indexedDB是浏览器中存储结构化数据的一种数据库,它不是用表结构来存储,而是用对象来保存数据.

创建对象存储:

```javascript
var user = {
  username : 'TOM',
  userage : 16
}
var store = window.indexedDB.createObjectStore('users', {keyPath : 'username'});
store.add(user);
```

上面表明创建一个users存储对象,并且用`username`做主键.

indexedDB还有事务的概念,想要修改或读取数据,都要通过事务来组织操作.

```javascript
var transaction = window.indexedDB.transaction('users');
```

这样就获取到了users这个存储对象.

详细事务操作方法看MDN的API介绍.

### 24.Web Worker

Web Worker是浏览器实现js后台运算的一种方法,具体实现方式浏览器有所不同,可能会用线程,或者后台进程等方式.

一个worker所执行代码的作用域与使用它的页面的作用域完全不同,是两个作用域.之间的通信要用postmessage和onmessage来处理.

下面是一个进行排序的worker实例.

```javascript
var data = [2,8,43,4,34,6,3,21,32,5,4,643,5,32,412];
    worker = new Worker('sort.js');
worker.onmessage = function() {
  ...
  // 后续处理
}

worker.postMessage(data);
```

sort.js就是进行排序的一段代码.

