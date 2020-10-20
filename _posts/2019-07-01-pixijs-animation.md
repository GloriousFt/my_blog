---
layout: post
title: "基于PixiJs的H5动画制作"
date: 2019-07-01 10:55:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- pixijs
- tweenjs
meta:
  _edit_last: '1'
---

# 实现效果
初始页面
一进入页面就直接显示动画场景，没有资源加载过渡页。随着用户长按按钮，进入正式场景（以下每个不同用户交互动画简称场景）。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/start-animation.gif)

场景一：溏心蛋会随着用户手势右移而按照固定轨迹移动，随后变成月亮。

场景二：小女孩的“烦恼”会随着用户左右滑动而逐渐消失，同时小女孩还会有个打滚动画。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/tree.gif)

场景三：一棵树会随着用户缩小的手势而变小，到一定程度后会变成西兰花。

场景四：风筝会随着用户放大手势而变大，到一定程度后变成摇尾巴的狗狗。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/dog.gif)

场景五：
猫头鹰叼着的信件会随着用户向下滑动的手势而下落，随后信件展开显示具体内容。

场景六：
小女孩追的蝴蝶是可以点击的，点击后小女孩和蝴蝶都会进行移动。

# 单屏动画适配
单屏H5动画的一个难点就在于移动端机型的适配，如果时间条件允许而且还有设计师来做图的话，用两套坐标和两套图来适配3dpr和2dpr的屏幕是最好的。

但是对大部分情况来说，往往都是一套图来适配不同的屏幕。

这种情况下，"基准"就很重要了。

"基准"是对于整个单屏布局的起点和方向，这往往是由业务需求本身决定的。如果"基准"在顶部，那么所有的主内容都要以上边为起点，向下排列，当遇到长屏幕时（如iPhone X），iPhone多出来的部分就只显示背景部分，不会有可交互部分。

如果业务需求是垂直居中的话，那么全部的元素都要以中间为"基准"向两边排列，长屏幕时内容等比向两边扩展。

同理，如果以底部为"基准"的话，那么在长屏幕时就要在上边流出非交互区域只做显示用，用单屏背景来补充长屏幕的空缺。

总结一下就是，用一个较长的背景图做背景，然后选择基底来让背景以不同方式展示。那么背景图要有多长呢，这就取决于你要适配的最长设备的屏幕了。

### 背景图尺寸
以iPhone X为例，iPhone X的逻辑分辨率为375 * 812。而我们与设计师沟通的设计稿通常都是750 * 1334像素的，换算一下也就是375 * 667的。所以当我们按照设计稿做出来一个单屏页面时，在iPhone X上显示就会有145像素（812 - 667）的空缺，如下图所示的extra space。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/compare.jpg)

为了填补这一空缺，就需要让设计师填充一些元素在背景图上，但是怎么填充就是上面提到的基准决定的了。如果选择垂直居中，那么就需要在上下同等填充内容。这样，即使在小屏幕的设备上，屏幕中依然能够显示中间重要的信息部分。

那么怎么决定用什么样的基准呢？

### 基准场景
比如下图的遮罩提示，从设计的角度来说，为了符合人类默认行为，肯定会把这种提示放在离屏幕下面一定的距离，让用户用拇指直接就能触到。不管用户的移动设备多长，设计师都希望用户伸出手指就可以碰到，所以这个场景下，这个提示就是以下边为基准的。所以在适配的时候就要全部以下面为基准定位坐标。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/stage1.png)

再比如下图的蓝天和草地的场景，设计师希望天地交界线就在屏幕的中间，那么就需要全部元素都垂直居中，这样不管设备屏幕大小，动画的元素都是基于这条"交界线"的。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/stage2.png)

其实最常见的还是以顶边为基准的场景，比如关闭、后退这些按钮，通常都是要放到屏幕顶部的，如下图所示。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/stage3.png)

### canvas适配
有了以上的基础，就可以开始做正式的适配了。

整体的适配思路是这样：

1. 最外层的Pixi应用大小就为屏幕大小。
```javascript
// 初始化pixi应用
this.app = new PIXI.Application({
    width: WINDOW_WIDTH,
    height: WINDOW_HEIGHT,
    resolution: window.devicePixelRatio,
    antialias: true,
    backgroundColor: 0xffffff
});
```
WINDOW_WIDTH个WINDOW_HEIGHT为屏幕的宽高，这里定义为常量，用window.innerWidth和window.innerHeight获取。

2. 应用内部搭建自适应动画舞台stage。

舞台大小就是设计稿大小，然后设置其scale，让它的大小能适应屏幕的宽高。
```javascript
this.rootStage = new PIXI.Container();
this.rootStage.width = DESIGN_WIDTH;
this.rootStage.height = DESIGN_HEIGHT;
this.rootStage.scale.set(WINDOW_WIDTH / DESIGN_WIDTH);
this.app.stage.addChild(this.rootStage);
```
这样做的一个好处就是，对于舞台上的每一个元素的坐标和尺寸都是可以计算好的固定值（基于设计稿）。然后通过舞台的scale来适配。

但此时出现了上边说的长屏幕适配问题，解决它的方法就是选择一个基准，然后用一个长图做背景。这样，即使在iphoneX上看仍然可以很好得显示。但是要注意，用户的交互区域也一定是跟着基准走的，不要把交互区域做到舞台外。

比如垂直居中的话，那么上边和下边多出来的地方就尽量不要放置可交互的舞台元素了，不过仍然可以放置显示元素。

# 逐帧动画

逐帧动画是利用tweenjs和HTML5的requestAnimationFrame方法实现的。

```javascript
animate();

function animate() {
    requestAnimationFrame(animate);
    // [...]
    TWEEN.update();
    // [...]
}
```

[tweenjs](https://github.com/tweenjs/tween.js/blob/master/docs/user_guide.md)是一个顺滑改变对象某些属性的库，利用这点可以实现很多动画效果，如淡入淡出，平移，抖动，旋转和大小变换等动画效果。

利用这些动画效果就可以拼装出来整个页面的动效，比如开头起始页。

一进入起始场景，文字开始淡入淡出，背景的云一直在循环漂浮。然后用户长按按钮积攒进度条，同时背景迅速滚动，当进度条满时，画面淡出，淡出过程中背景继续在滚动，然后正式进入第一个互动场景。

云朵循环漂浮效果其实是用了两套一样的云朵图实现的，两套图向上缓慢移动，当第一套图移出屏幕区域，同时位移大小是第一个图的高度时，让第一套图移动到第二套图的起始位置，这样就形成了一个循环。

下面就是封装好的元素平移方法。

```javascript
_move(sprite, position, duration, easing, callback) {
	let oldPosition = {
		x: sprite.position.x,
		y: sprite.position.y
	};
	let tween = new TWEEN.Tween(oldPosition)
		.to(position, duration)
		.easing(easing || TWEEN.Easing.Cubic.InOut)
		.onUpdate(function() {
			sprite.position.set(oldPosition.x, oldPosition.y);
		})
		.onComplete(() => {
			tween.stop();
			if (callback) {
				callback();
			}
		});
	return tween;
}
```

这里对按钮长按的监听是pixi元素的pointerdown的监听，每次pointerdown都会触发进度条加载，当用户松手时，会触发pointerup，这时取消加载进度。

在进度条加载过程中，背景也是有个动画效果的，快速向上滚动，这个其实也是用tweenjs对背景的container做一个向上平移。最后在进度条满了时对整个container做一个淡出，同时第一个场景淡入，以上就是整个初始页的过程。

因为其他场景的动画基本都是用tweenjs封装的动画方法拼凑的，所以这里就不一一赘述了。

重点就是，**用tweenjs改变pixi元素的alpha、position、scale的值来实现动画效果**。

# 性能优化
性能优化是动画实现中最为重要的一环，下面主要从“资源加载”，“资源销毁”两个方面来说明本项目中做的性能优化。
### 动态加载资源
在大多数的动画项目中，往往都是需要加一个loading页面，在这个页面中进行后续资源的加载，这些资源包括音频、图片、字体等静态资源文件，有时可能还会读取服务器的数据。这是业内比较通用的一种做法，但是同时也带来了一些不好的用户体验，比如用户等待的时间较长，用户无处安放的小手可能会直接关闭页面。或者加载的资源没有全部都利用到，用户操作了一段时间就关闭了页面，导致移动端宝贵的资源加载浪费。

所以，为了给用户带来更好的体验，我把所有资源按场景进行了分割，只在用户需要的场景提前加载资源。

例如在页面初始化的时候，其实页面是只加载了蓝天白云这几个图片资源的，文字效果淡出，等待用户长按按钮的时候，第一个场景的资源才开始加载。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/test1.png)

当正式进入第一个场景时，页面会加载下一个场景的资源，这样以此类推，直到最后一个场景。
![](https://raw.githubusercontent.com/GloriousFt/my_blog/gh-pages/assets/img/test2.png)

在资源处理上其实还可以做一些工程上的优化，比如小的图片转化为base64的，大的图片剪切成两个或多个图片拼接显示，这个主要是因为过大的图片可能会在加载时一点点显示，体验不好，而且还有可能canvas绘制不出来，用pixijs时，在绘制大的长图时会黑屏。
### 销毁资源
用pixi绘制动画时，如果当前canvas上有以后用不到的元素时，最好是直接从canvas上移除，不要只是隐藏。

场景切换时都是有个淡入淡出效果过渡的，资源销毁就可以放在这一步。在新进入一个场景时，新场景的元素开始绘制，同时加载下一个场景的资源并销毁上一个场景的资源。pixi提供了destroy方法，对最外层的场景容器进行销毁，同时也会销毁其子元素。

```javascript
TWEEN.removeAll();
if (buttonContainer.sceneContainer) {
	buttonContainer.sceneContainer.destroy(true);
	buttonContainer.sceneContainer = null;
}
that._enterNextScene();
```

tween的removeAll方法是移除所有tweenjs动画，也就是所有需要tween的update的动画都会被移除。这与销毁pixi的元素资源还不太一样，销毁资源是让canvas不再考虑绘制这些内容，而tween的清除是不再监听对象的属性变化。

# 总结
### 部分Android小屏幕机型问题
其实现在的单屏适配方法在小屏幕上还是可能会出现内容被截取的情况，小屏幕是指换算到750屏幕宽度后，屏幕长度小于1334的。虽然能保证大部分移动设备可以正常显示，但是还是特殊的设备会显示不全。

所以可能还是要对一些设备做特别的媒体查询，来进行特别适配。

一套图对单屏页面的适配目前还没有简单的特别完美的解决方法，要么是做媒体查询，要么是根据基准做背景调整。

以上就是作者的一些动画实现思路的总结，欢迎留言讨论。
