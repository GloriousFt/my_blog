---
layout: post
title: AngularJs各Controller之间的通信
date: 2016-03-05 15:40:27.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- AngularJs
meta:
  _edit_last: '1'
---
### 在AngularJs的Controller之间传值通信
AngularJs里面Controller的通信基本是$emit,$broadcast,$on.

>  $emit是子控制器向父控制器发通信的方法.
>  $broadcast是父控制器向子控制器发通信的方法.
>  $on是监听方法,用于监听通信.

```html
<div class="home" ng-controller="homeCtrl">
  <div class="firstChild" ng-controller="firstCtrl">
    <button ng-click="to-home()">第一个子控制器</button>
  </div>
  <div class="secondChild" ng-controller="secondCtrl">
    <button ng-click="to-home()">第二个子控制器</button>
  </div>
</div>
```

<!--more-->

假设有一段html如上,所以控制器作用域的关系为:

* homeCtrl
    * firstCtrl  //第一个控制器
    * secondCtrl //第二个控制器

如果第一个控制器想给homeCtrl传值,需要在homeCtrl里做监听,也就是$scope.$on.
同时,要在firstCtrl里做发射,也就是$emit.

```javascript
//firstCtrl.js
$scope.to-home = function(){
  $scope.$emit('firstToHome', {
    something : 'value',
  });
};

//homeCtrl.js
$scope.wanted_data = '';
$scope.$on('firstToHome', function(event, data){
  $scope.wanted_data = data.something;
});
//这样,就把value传给了homeCtrl的wanted_data.
```

如果第二个控制器想给第一个控制器传值,需要注意,**平级之间的控制器是没办法直接传值的**,所以可以先传给父控制器,再广播给子控制器.
需要在homeCtrl里做监听和广播.
同时,要在secondCtrl里做发射,firstCtrl里面做监听.

```javascript
//secondCtrl.js
$scope.to-home = function(){
  $scope.$emit('secondToHome', {
    something : 'value',
  });
};

//homeCtrl.js
$scope.wanted_data = '';
$scope.$on('secondToHome', function(event, data){
  $scope.wanted_data = data.something;
  $scope.$broadcast('homeToFirst',{
    something : $scope.wanted_data,
  });
});

//firstCtrl.js
$scope.final_data = '';
$scope.$on('homeToFirst', function(event, data){
  $scope.final_data = data.something;
});

```
这样,就把secondCtrl的'value'传给了firstCtrl的final_data.
