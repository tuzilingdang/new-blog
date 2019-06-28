---
title: JavaScript高级技巧-函数防抖与函数节流
date: 2018-01-18 20:35:13
tags: 高级技巧
---

## 问题
在日常开发中，经常碰到这样的问题： 比如在支付时连续快速的点击支付按钮，在安卓机上会弹出好几次支付密码框。 不停地点不停地点，在有些安卓机上，页面直接卡住了。iOS手机很稳，什么问题都木有。

这就需要在一段时间内，点击事件不停的被触发时，需要控制住回调函数不要一直执行，当操作停止时。这里就用到了函数防抖。

如果不是这种多次点击的提交事件，比如是window.resize,或者滚动的时候，需要降低事件发生的频率呢？这里和前面有所不同，用到的是函数节流。


## 函数节流与函数防抖的比较
文字描述比较难以理解两个过程分别发生了什么，区别在哪里。那就上代码吧。

<iframe height="526" style="width: 100%;" scrolling="no" title="函数防抖/截流Demo" src="//codepen.io/April666/embed/VJywMr/?height=526&theme-id=dark&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/April666/pen/VJywMr/'>函数防抖/截流Demo</a> by tuzilingdang
  (<a href='https://codepen.io/April666'>@April666</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

代码中实现的是，记录鼠标move监听事件的触发频率，每次触发我们就在对应的区域内通过Dom操作来增加一个有颜色的竖线。DOM操作，并且通过appendChild增加孩子节点，在渲染过程中是非常耗性能的。
可以看到，当我们每次在区域内移动鼠标时，不加控制的话，触发事件的频率非常高，每一次竖线都对应了一次DOM操作。 而通过函数防抖（Debounce），在第二幅频率图中可以看到，每次move停止后才会绘制一条竖线。 通过函数节流（throttle），在第三幅图中，每次move的过程触发事件并没有停止，而是降低了执行的频率。 很明显，函数防抖每次连续操作只执行了最后一次，而函数节流的方法，每次连续操作，回调函数并没有停止执行，而是隔一小段时间执行一次，大大降低了执行频率。

## 函数防抖

### 常见场景

* 点击form的提交按钮
* 支付流程点击支付按钮
* input输入框输入字符/数字等的检验操作
* 键盘按键的keyup事件等

### 实现原理

通过闭包的特性，设置一个定时器，传入一个delay延时时间，在延时时间内原函数不会执行。 而debounce函数不停被执行时，设置的定时器timeout由于闭包函数的原因，还存在在内存当中，每次闭包执行的时候都会清除这个定时器，传入的fn函数一直不能执行。 知道闭包函数不再执行了，定时器的delay延时时间到了，再去执行里面的fn。代码如下：

```js
function debounce(fn, delay) {
    let timeout
    delay = delay || 500
    return function () {
        let context = this
        let args = arguments
        clearTimeout(timeout)
        timeout = setTimeout(function () {
            fn.apply(context, args)
        }, delay)
    }
}
```



## 函数节流
###常见场景

* window.resize窗口大小监听的事件处理
* 页面滚动scroll的事件处理 
* 动画特效的节流控制 等
 
### 实现原理
控制需要执行的代码，不让它在没有间断的情况下连续触发重复执行。所以，需要创建一个定时器，给一个间隔时间，在这段时间内不能运行代码。然后，点击事件重复触发时，给一个clearTimeout，清除之前设置的定时器，然后重新设置一个。
这里的意思是，如果前一个定时器还没有被触发，那直接被清除设置成新的，第一次定时的代码也不会执行。 假设被触发了，已经是过了一段时间了。这样就在一定时间内，控制了用户不停操作带来的代码段重复执行的问题。

### 初步方案
《JavaScript高级程序设计》中给出了解决方案，代码如下：

```js
function throttle(method, context) {
	clearTimeout(method.tId);
	method.tId = setTimeout(function(){
		method.call(context)
	}, 100)
}
```
这里，第一个函数是要执行的函数，第二个是执行的作用域。

### 升级方案
```js
function throttle(fn, interval) {
    let timeout
    interval = interval || 160
    let nextTime = (new Date()).getTime() + interval
    return function () {
        let context = this
        let args = arguments
        let now = (new Date()).getTime()
        if(now < nextTime) clearTimeout(timeout)
        else {
            nextTime = now + interval
            timeout = setTimeout(function () {
                fn.apply(context, args)
            }, 0)
        }
    }
}

```
