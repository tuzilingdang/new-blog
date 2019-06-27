---
title: JS事件(2)-touch事件的兼容问题

date: 2017-03-08 18:02:00
tags: touch事件
---


开发移动端Web网页时，对于手持设备，点击、滑动、双击等均会用到touch事件。很多简单的情况下，对于点击事件，直接用对click做一个监听，也可以实现触发相应的事件，对该事件做一个回调函数的处理。

对于稍微复杂的情况，直接绑定一个click并不是一个好方法。 最近就碰到这样一个问题。

### 遇到问题

项目中有一个轮播图， 用了swiper插件，代码是别人写的，写法较为混乱。做兼容测试时发现iOS上很好用啊，到了安卓机上就各种问题， 放到浏览器上测试下也出现这种问题。  具体就是，正常轮播图点击时可以进入图片对应的页面，用户滑动图片时图片相应滑动。 但在安卓机和电脑上发现，每次滑动时会直接误判为点击操作，导致用户不能进入图片链接。

就这样的轮播图：

![43](/img/JS事件-2-touch事件的兼容问题/1.png)

### 发现问题
直接用真机调试看看情况，发现除了使用swiper插件，对每个li元素，同时绑定了click事件，通过回调函数来做链接跳转。初步原因判断，可能是代码执行时，还没有开始滑动就先执行了click事件。不管怎样，同时写click暴力解决跳转问题，这样的写法明显是非常不合理的。

![43](/img/JS事件-2-touch事件的兼容问题/4.png)

不管啥原因，还是先来回顾一下touch事件。


### 回顾设备事件

#### 触摸事件
触摸事件有这么四种：

1. touchstart
2. touchmove
3. touchend
4. touchcancel

Dom规范中并没有定义触摸事件，但是他们都是可以兼容DOM的，所以鼠标事件常见的属性我们也可以在touch中看到。具体可见本次试验截图。

![43](/img/JS事件-2-touch事件的兼容问题/6.png)

加上鼠标操作，按照《高级程序设计》写的，这些事件发生的顺序是这样的： touchstart -> mouseover -> mousemove -> mousedown -> mouseup -> click -> touchend 。  有些博客上写的顺序是 touchstart -> touchend -> mouseover -> mousemove -> mousedown -> mouseup -> click 。 有点混乱了。

但是实验起来，不同系统对touch事件的支持并非是一致的。 国外大神做过全面的测试， 我们可以看到图中的结果。更具体的结果可以参考他的测验结果：

[https://patrickhlauke.github.io/touch/tests/results/](https://tuzilingdang.github.io)

对于目前使用较多的iOS（8、9暂时没有）及安卓系统版本，引用图片如下：

![43](/img/JS事件-2-touch事件的兼容问题/2.png)

![43](/img/JS事件-2-touch事件的兼容问题/3.png)

这样看起来，click应该是touchend后发生的。 并且，在移动设备中，click事件有个比较重要的延时300ms的问题。这样应该不可能是因为安卓系统中，click事件早于touchend发生，而导致先执行了这段代码。

再补充下300ms的问题，因在手持移动设备中，双击会有页面放大的效果。 双击的时间是有间隔的，所以必须在第一次点击时，延时300ms 来判断用户是否会紧接进行第二次点击。 zepto有较好的解决方案，一般使用tap来代替click避免延时问题。


#### 手势事件
虽然跟本次的问题无关，还是顺带提一下手势事件，有这么三种:

1. gesturestart
2. gesturechange
3. gestureend

三个事件的触发条件是两个手指同时在一个元素范围内。

##### 手势事件与触摸事件的关系:

 当一个手指先放在屏幕上，touchstart事件触发 -> 另一个手指放上来时，gesturestart -> 任一手指移动，gesturechange -> 任一手指离开屏幕，
gestureend -> 触发该手指的touchend事件 。

##### 手势事件与触摸事件的不同:
多了两个属性: rotation 和 scale。 rotation表示手指变化的旋转，正值顺时针，负值逆时针。 scale代表手指间距离的变化。

好了，啰嗦到此，排除click执行顺序问题，那很有可能是touch时间iOS和安卓的一些支持不同，也就是安卓很可能不能准确判断touchmove。 还是那手机来测试一下。

### 真机试验

首先，改造下代码，看看touchmove这一步是怎么执行的。


![43](/img/JS事件-2-touch事件的兼容问题/5.png)

写一个touchmoveFlag，判断是否进入了touchmove。分别用iPhone和安卓机进行测试，发现单纯做点击操作时，iPhone很正常的直接略过，touchmoveFlag的值为false没有变。 然而安卓每次点击都会直接出发touchmove，变为true。  

所以，安卓对touchmove的判断相当的不准确啊。

所以现在目标问题就是，怎么在安卓touchmov任何操作都触发的情况下准确区分出点击和滑动。 touchmove不管用，那就只剩距离判断了。

憋出大招，用事件的targetTouches属性来计算滑动距离，代码如下。

	var startPoint = {}, endPoint  ={};
    $("#vipList .swiper-container ul li").unbind()
    .on('touchstart',function(e){
    	startPoint.x = e.targetTouches[0].clientX;
        endPoint.x = e.targetTouches[0].clientX;
    })
    .on('touchmove',function(e){
    	 endPoint.x = e.targetTouches[0].clientX;
    }).on('touchend',function(e){
    	var moveDistance = startPoint.x - endPoint.x;  
        console.log("moveDistance:"+moveDistance);
    }

试验结果如下：

##### 安卓下的点击和滑动的移动距离

1. 点击

![43](/img/JS事件-2-touch事件的兼容问题/7.png)

大于7的都是幅度比较大的点击，有的是指头横按一下可能会造成多点触碰距离会大于10，20.

2. 滑动

![43](/img/JS事件-2-touch事件的兼容问题/8.png)

向左滑动为整数，向右滑动为负数。 正常的滑动都在绝对值30 以上。

##### iOS下的点击和滑动的移动距离

不得不说，iOS对触摸事件的精确度真的是相当高啊，测试完比安卓准确很多，基本无误差。
前面小圆点中的是点击次数。

1. 点击

![43](/img/JS事件-2-touch事件的兼容问题/9.png)
基本所有正常的点击，其中连续点击了28次移动距离全部是0. 绝对值大于10的都是很夸张的情况了。


2. 滑动

![43](/img/JS事件-2-touch事件的兼容问题/10.png)

滑动也感觉触点距离很准确呢。


### 解决问题

最后代码变成这样：

![43](/img/JS事件-2-touch事件的兼容问题/11.png)

在真机上测试一遍，非常完美，各种机子上都没问题了。






