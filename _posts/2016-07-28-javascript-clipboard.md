---
layout: post
title: "Javascript对剪贴板的操作(safari和chrome)"
date: 2016-07-28 15:09:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript
- Clipboard
meta:
  _edit_last: '1'
---
## 1. 获取剪贴板

不同的桌面应用可以将各种数据复制到剪贴板中,那么从Js层面来说,它应该如何拿到系统剪贴板的数据呢.

首先,js要监听相应的事件,比如,我要在一个web应用编辑器中来操作剪贴板,那么,这个监听就应该是整个document的事件.

```javascript
$('body').addEventListener('paste', function(event){
    event.preventDefault();
    ...
});
$('body').addEventListener('copy', function(event){
    event.preventDefault();
    ...
});
```
一共有beforecopy,beforecut,beforepaste,copy,cut,paste这些事件可以绑定在任意的标签上.

event中有一个属性,clipboardData,它就是系统剪贴板的数据.无论是copy还是paste,其实都是对它的操作.

clipboard有如下的属性:

属性           | 类型          | 说明
------------- | ------------- | ---------------------------
dropEffect    | String        | 默认为none
------------- | ------------- | ---------------------------
effectAllowed | String        | 默认为uninitialized
------------- | ------------- | ---------------------------
files         | Obejct        | 默认为{length:0, item: function}
------------- | ------------- | ---------------------------
types         | Object(Array) | 默认为{'text/html':...}

**其实还有items属性,不过只有chrome和opera支持**

<!--more-->

## 2.获取剪贴板数据

现代浏览器clipboardData都有了getData方法,开发者可以根据不同的类型来获取不同的数据了.
比如,想要html类型的数据:

```javascript
var htmlData = clipboardData.getData('text/html');
```

**getData**方法就是根据types来获取数据内容,上面代码获取的就是一段html的字符串.

通常剪贴板的types是由不同浏览器和不同应用来自行设置的.

比如你在QQ中复制一段图片和文字,然后在浏览器中某个编辑器应用上粘贴,你会发现剪贴板数据中还有

```javascript
TencentAttributeStringType
```

这个类型的数据,这很明显就是tencent自己定义的数据类型,然后方便它的应用进行复制粘贴操作解析.

当然也有默认的一些数据类型,通常来说会有
```javascript
['text/html','text/plain'...]
```

如果你是mac用户,你还能发现mac系统自己定义的一些数据类型如
```javascript
['com.apple.webArchive'...]
```

不同的类型导致web程序开发时候极其不方便,比如一个web应用想实现对复制数据中图片的处理,那一般来说会去找html类型数据,然后找出所有的img,这个不难.

但是在不同情况下,'text/html'并不是一定会出现的,比如safari浏览器下,用户对网页内容进行复制后,剪贴板中是不会出现'text/html'类型的数据的.

通常对safari的剪贴板数据中HTML类型数据的获取,都是'webArchive'类型.

简而言之一句话:

> WebArchive is a class for HTML in Mac.

这个类包含了整个的html信息,不过只有mac中能解析,也就是说不是OC或者Swift层面的就动不了它了.

在Js中能尽量全面的获取到剪贴板数据,我的做法是对类型做一个优先级判断:

```javascript
var data = clipboardData.getData('text/custom-data');
if (data) {
    ... // 处理获取到的剪贴板数据
    return;
}

data = clipboardData.getData('text/html');
if (data) {
    ... // 处理获取到的剪贴板数据
    return;
}
... // 后续的优先级判断,获取数据
```

首先要知道,数据类型都是并列关系的,也就是说剪贴板中所有数据是可以通过一种类型拿到的,而不是多种类型组装成剪贴板中的数据.

但是极有可能是剪贴板中的数据有多个类型,比如你在mac下复制一个MS word中的内容,你会发现它有自己定义的类型,还有html,还有plain,所以获取时是完全看应用需求的.

代码中'text/custom-data'是web应用自行设置的一种数据类型,也就是说如果你想保证你应用的自定义类型的数据能够在剪贴板中被获取到,那么一定是优先获取自定义的数据类型的.

至于如何设置到剪贴板中后面会说.

然后再判断是不是html类型的,当然这个根据应用的需求了,也有可能纯文本优先级更高.如果获取到了就返回,不再做后续判断.

这个方法好处就是能够根据应用的数据处理优先级来获取剪贴板数据.

缺点就是一旦复制的数据比较奇怪,剪贴板数据类型没有被程序所写的优先级关系遍历到,那么获取数据就会失败.

## 3.设置剪贴板数据

上面讲了获取数据getData的方法,同样的,对系统剪贴板数据的设置js层面也是可以做到的.

```javascript
    var data = '<div style="color:green;">clipboard</div>';
    clipboardData.setData('text/html', data);
```

setData方法两个参数,第一个为数据类型,第二个为放到剪贴板的数据.

什么地方会用到设置剪贴板数据呢,设想一种情况.

web应用中定义了一种结构数据,只有应用自己知道这种数据怎么处理,当我们在页面上复制一段内容时,其实应用想要的是这段数据带有结构的,不然破坏了结构,按照默认的copy方法,数据可能都变成了html字符串.

这种情况在表格内容复制时最为突出,如果web应用自己定义了表格的实现方法,那么默认复制出来的数据一定不能被应用再次很好的使用.这就需要自己来设置剪贴板数据,让剪贴板直接存储我们想让它存的内容.

比如,我们想要存储json格式的数据,就可以先把数据stringfy,然后放到'webname-json'中,等在读取数据时再JSON.parse下就ok啦.

这也就是为什么QQ,MS word等应用有自己定义的数据类型.只不过现在所讲的都是js层面的,那些应用都是OC,Swift层面的.

当不同应用对剪贴板操作,那么问题就来了,一个应用自私地获取了剪贴板的数据,并且修改了它,那么其他应用在使用剪贴板数据的时候必然会出现问题.

重要的是,用户可能都不知道为什么他复制后,粘贴出来的内容是不对的,这时他就会对这两个应用同时产生负面疑问.

所以,我们在对剪贴板数据处理时,尽量不要破坏它默认已有的数据类型,自定义一种数据类型一定是最好的.