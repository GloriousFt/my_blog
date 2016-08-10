---
layout: post
title: "Nodejs+Express+Forever的服务器端入门"
date: 2016-08-10 15:57:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript
- Nodejs
meta:
  _edit_last: '1'
---
## 1.安装配置环境

### 1.1 node.js
首先,安装nodejs,直接下载安装就好,无需配置 [Nodejs下载](https://nodejs.org/en/).

nodejs安装好之后,npm也就安装好了(Node.js在0.6.3版本之后内建npm).

npm就是node package manager,就是一个node的套件管理.

### 1.2 express
express是node.js的一个web应用程序框架,提供了很多强大的API来构建一个web应用.

安装express,在项目目录下执行

```javascript
npm install express
```

至于指令加不加--save等参数就是看项目本身了,在这里不讲解,可查看npm了解详情.

<!--more-->
### 1.3 forever
forever是用来维持一个node脚本持续运行的.如果只是用node来执行一个xxx.js,那么当远程ssh失去连接的时候,这个脚本就会停止,forever就是让这个脚本连续保持运行以实现服务器功能的.

安装它

```javascript
npm install forever
```

启动它

```javascript
forever start xxx.js
```

其中xxx.js就是服务器端程序.如果想关闭forever,运行

```javascript
forever stop xxx.js
```
更多的命令可以查看

```javascript
forever --help
```

### 1.4 nginx安装配置(可选)
如果是做一个网站需要80端口的,那么可以安装一下nginx,将80端口映射到自己定义的一个端口,这个端口就是node.js里监听的端口.

mac下用homebrew安装nginx

```javascript
brew install nginx
```

然后修改配置,在location项下面加

```javascript
proxy_pass http://localhost:8081;
```

端口号与xxx.js中监听的一致.

## 2.一个简短的服务器端程序
在项目目录下新建一个test.js文件.

写个最简短的helloworld吧.

```javascript
var express = require('express');

var app = express();

app.get('/helloworld', function (req, res) {
    ...//具体处理逻辑
});

var server = app.listen(8081, function () {
    var host = server.address().address;
    var port = server.address().port;
    console.log("应用实例，访问地址为 http://%s:%s", host, port);
});
```

这样一个简单的程序就已经可以让服务器运行起来了,浏览器中访问localhost:8081/helloworld就可以看到一个空白页面,同时服务器的console还会出现访问地址.

express可以get或者post,上面的代码就是定义了一个接口,当url与之匹配时就会访问这段程序.

app同时要监听一个端口,这里定为8081,所以要是网站应用默认为80端口的话,用nginx代理一下就行.

那如果url中带有参数怎么获取呢?

例如,我们要访问的url为

"http://localhost:8081/helloworld?id=2&name=lili"

```javascirpt
app.get('/helloworld', function (req, res) {
    var id = req.query.id,
        name = req.query.name;

    console.log('id:' + id + ',name:' + name);

    res.json({"id":id, "name":name});
});
```

可以看到,get方法有两个参数,第一个为地址,第二个为回调函数.当地址匹配时,就会执行这个回调函数.

回调函数也有两个参数,req为请求,也就是url相关的,利用req的查询方法就可以获取到url中的参数.

第二个参数为res,也就是返回,这个接口返回的数据就是这个.

上面的代码会返回调用的url的参数组成的json.

具体的express的方法参看[express文档](http://expressjs.com/zh-cn/4x/api.html).

## 3.后续学习
会了简单的服务器端搭建,后面就要学习具体的方法了.

搭建一个web应用,express是非常好用的.

如果是写一个回调数据接口,可能还需要用到async模块来处理同步异步的问题.

如果这个接口还要访问其他的接口,那么还要有request模块.

总之,这只是node.js写服务器端的一个小小例子,如果要做复杂一些的应用,还要学习很多东西.