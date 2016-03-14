---
layout: post
title: 在Yii2.0环境下,前端AngularJs中的$http服务配置与使用
date: 2016-03-14 17:30:27.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AngularJs
- Http
- Yii2.0
meta:
  _edit_last: '1'
---

现在前后端的异步通信用的最多的应该算是Ajax了吧, JQuery和Angular中都有提供相应的服务.

* $.ajax()
* $http()

默认配置下,它们最大的不同就是Angular把数据作为payload(json)数据传送,而jquery把数据作为form data(在url中)传送.

这两种方式下,服务器端接收数据的方式是不同的,以PHP为例,对form data的接收直接$_POST就可以,而对payload数据则要用

```php
json_decode(file_get_contents("php://input"), true);
```

在AngularJs的$http服务中也可以配置request的header,这样就可以统一后台接收数据的方法,而不用写两套接收数据的方法.

```javascript
app.config( ['$httpProvider', function($httpProvider){
    $httpProvider.defaults.headers.post['X-Requested-With'] = 'XMLHttpRequest';
    $httpProvider.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
    $httpProvider.defaults.headers.post['Accept'] = 'application/json, text/javascript, */*; q=0.01';
}] );
```

这个其实只改content-type就好,其它只是把request的header改成跟jqeury的ajax服务一样.

**在Yii2.0中,csrf防护是自动开启的,所以要是没有传token给接口的话,那么要在config.php里配置一下components**

```php
'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
            'cookieValidationKey' => 'XK-***********************',

            // this is for normal posts and requests,
            // if  'enableCsrfValidation' is true, many requests will be blocked with 400 error.
            'enableCsrfValidation' => false,
            // this parser make yii's request->post() possible to deal with json data.
            'parsers' => [
                'application/json' => 'yii\web\JsonParser',
                'text/json' => 'yii\web\JsonParser',
            ],
        ],
```

parsers是让Yii2.0的接口能够自动加上对请求的判断,就不用加上开发者自己写的request判断了,也就是说angularjs不用再单独配置$http服务了,Yii2.0帮开发者做好了.

根据header里的Content-Type来更改这个地方的parsers吧.

$http的使用非常简单

```javascript
$http({
  method: 'GET',
  url: '/someUrl'
}).then(function successCallback(response) {
    // this callback will be called asynchronously
    // when the response is available
  }, function errorCallback(response) {
    // called asynchronously if an error occurs
    // or server returns response with an error status.
  });
```

response对象拥有以下属性.

* data - 就是接口返回的数据
* status - HTTP的状态码
* headers - 头部获取函数
* config - 生成这个请求的配置的对象
* statusText - 对应HTTP状态码的文字描述

$http还有几个快捷方法,其实就是封装了一些固定配置的方法,比如$http.post(),就是上述完整调用中method为POST的方法.

有关具体的$http方法,可以见[AngularJs的$http官方文档](https://docs.angularjs.org/api/ng/service/$http#head).

