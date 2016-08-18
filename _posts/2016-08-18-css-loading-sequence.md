---
layout: post
title: "浏览器对页面渲染时CSS优先级顺序"
date: 2016-08-18 17:45:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Javascript
- CSS
meta:
  _edit_last: '1'
---

## 1.不同属性style的定义

做一个页面样式定义优先级的实验,直接上代码

```html
<head>
    <link rel="stylesheet" href="css/index.css">
    <style type="text/css">
        .class1 {
            color : black;
        }
        .class2 {
            color : blue;
        }
    </style>
</head>
<body>
    <p id="test1" style="color:yellow;" class="class1">What color am I?</p>
</body>
```

外部样式index.css内容

```css
.class1 {
    color : green;
}
```

可以看到,这个页面的字体颜色是黄色的,也就是说标签上的style优先级最高.

当我们把style去掉时

```html
<p id="test1" class="class1">What color am I?</p>
```

页面的字体颜色变成了黑色,说明直接定义在head中的css样式优先级高于外部样式的.

我们可以继续把style标签中的class1删掉,会发现字体变成了外部样式定义的绿色.

所以得出结论:

**标签style属性样式定义 > head中style定义 > 外部样式定义**

<!--more-->

## 2.相同属性style,且相同class的定义

当我们在一个文件中定义了一个属性多次,那么优先级是什么样呢?

```css
.class1 {
    color : black;
}
.class2 {
    color : blue;
}
```

```html
<p class="class1 class2">What color am I?</p>
<p class="class2 class1">What color am I?</p>
```

可以看到,两行字都是蓝色的,说明css最终生效的与样式调用的顺序无关,而与定义的顺序有关.

所以得出结论:

**在同一位置css中定义的样式,定义在后面的样式优先级 > 定义在前面的样式优先级**

## 3.相同属性style,且不同class的定义

假如我们定义了一个css为

```css
#test1 {
    color : red;
}

.class1 {
    color : black;
}

p {
    color : blue;
}
```

```html
<p id="test1" class="class1">What color am I?</p>
```

可以看到,字体颜色为红色,说明id选择器的优先级定义最高.

再删掉id属性

```html
<p class="class1">What color am I?</p>
```

发现字体变成了黑色,说明类选择器的优先级定义高于标签名(元素类型)选择器的优先级.

所以得出结论:

**id选择器优先级 > class选择器优先级 > tag选择器优先级**

**当然用!important可以直接强制css定义优先级最高,但是极不推荐**.

理由如下:

* 用important会强制改变cascade,使用不当只会自食其果.
* 让css更加难以维护,当你发现你的css定义越来越乱的时候,你就知道important的坏处了.
* 不到迫不得已的情况下不要用,即便用了也请你尽早想办法替换掉,一个好的css规则的nature flow是不需要任何强制规定的.

[官方w3.org对css的cascade解释](https://www.w3.org/TR/2011/REC-CSS2-20110607/cascade.html#cascade)

这个的解释更清楚,包括浏览器user agent的定义都加进去了.