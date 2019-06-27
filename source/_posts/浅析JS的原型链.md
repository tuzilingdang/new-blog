---
title: 浅析JS的原型链
date: 2016-08-23 00:20:17
tags: 原型链,继承,对象
---


如果你学过Java，那你一定知道它是面向对象的语言。创建一个对象时，首先，你要用到类的概念。 而对于JavaScript，它不同于其他OO的语言，严格意义上来讲，称之为基于对象的语言。在ES6之前，JS中是不能直接使用类的，当然你也可以自己写一个类的方法，然后去调用。

那ES中怎样实现继承呢，这里就引出了一个概念：原型链。

### 定义

A prototype chain is a finite chain of objects which is used to implement inheritance and shared properties.

看完英文版定义大概可以知道原型链可以用来做什么了。当两个对象大部分属性都相同，只有一小部分不一致的时候，好的设计是我们更希望减少在每个对象中重复使用一样的功能。在像Java这些语言中，可以基于类的继承来实现。但是EMAScript中并没有类的定义，要实现类似的功能，就需要用到原型链了，也就是说，EMAScript中是基于原型的继承。

### 浅析原型链

直接上几行代码看一下：

	var animal = {
		move: 'run',
		runningSpeed: function(){
			return this.move + ' '+ this.speed;
		} 
	};
	
	var tortoise = {
		speed: 'slow',
		__proto__: animal
	};

	var rabbit = {
		speed: 'fast',
		__proto__: animal
	};

	console.log(tortoise.runningSpeed());	//run slow
	console.log(rabbit.runningSpeed());	//run fast
	
看完代码，是不是感觉很简单？这个例子中，tortoise和rabbit的原型是animal，animal有一个move的属性，这里给一个‘run’的值。 当tortoise和rabbit调用runningSpeed的方法时，因为他们的属性__prpto__指向了animal，他们直接可以调用animal的方法，而这个方法是两个小动物对象自己没有定义的。

也就是说，原型链的规则很简单，当一个方法或属性在一个对象中找不到时，那就顺着原型链去找吧。小兔子的prototype中找不到，就可以找到animal中，animal中找不到，就找animal的prototype。在这里要注意一点，this的指向问题。this的值是在一个对象调用original object时该对象的值，而跟找的的prototype中的方法无关。但在animal的runningSpeed方法中，this.move 的值就是animal中的‘run’了。

上一张图来帮助理解：

![prototype-chain](http://dmitrysoshnikov.com/wp-content/uploads/prototype-chain.png)

图1		prototype chain

### 练习
在js引擎执行时，所有存在的对象可以构成一棵原型树，根节点为Object.prototype。所以，写一个函数实现原型链的遍历的话，获取对象上面所有的原型应该怎么写呢？

答案在下面:

```js
	function getPrototypeChain(obj) {
		var chain = [];
		while(obj = obj.__proto__){
			chain.push(obj);
		}
		chain.push(null);
		return chain;
	}
```





