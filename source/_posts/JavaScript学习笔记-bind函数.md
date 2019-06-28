---
title: JavaScript学习笔记-bind函数
date: 2017-09-19 10:16:08
tags: javascript 
---


下面是官方文档的纯手工翻译：

> **Funciton.prototype.bind()**
 
bind()函数创建了这样一个函数：当被调用时，this关键字会被赋值到给定的value。当一个新函数被调用时，原本的一系列参数会在给定value的前面一起传递给新函数。

## 语法

> **fun.bind(thisArg[, arg1[, arg2[, ...]]])**


### 参数

> **thisArg** 

当被绑定的函数被调用时， 这个value被作为this参数传给目标函数。

**注意：**
**如果你使用 new 操作符对被绑定的函数进行构造时，这个值会被忽略**

> **arg1, arg2, ...** 

当调用目标函数时，这些参数会在其他参数前提供给被绑定的函数。

> **返回值** 

返回值其实是一个给定函数的副本，但是参数变成了**初始参数+this的value**


## 描述
bind()函数创建了一个新的绑定函数（**BF**）。这个**BF**是另外一个函数对象，它包含了原始的函数对象。调用一个**BF**时，通常就是执行了它包含的这个函数。
一个**BF**有这样几个内部属性:

* **[[BoundTargetFunction]]**	 -原始目标函数对象	

* **[[BoundThis]]**	 -当包含的函数对象对调用时，这个value作为this value被传递给函数

* **[[BoundArguments]]** -这些参数将在所有参数前面被传入

* **[[call]]** -执行此对象的相关代码。 传递到内部函数里的参数是**this value+通过调用表达式传入的参数列表**。

当被绑定函数被调用时，他调用了内部方法**[[Call]]**在**[[BoundTargetFunction]]**上。**[[BoundThis]]**	和**[[BoundArguments]]**作为参数传入。


## 实例

### 创建绑定函数
下面是一个简单的例子。初学js的jser经常犯的错误是从一个对象中取到一个方法，直接拿来调用，同时期望调用时的this指向原来的对象。一般来讲，最后都调用失败了。使用bind函数可以直接解决这个问题。

```js
	this.x = 9;
	var module = {
		x： 81，
		getX： function() {
			return this.x;
		}
	};
	
	module.getX();
	
	retriveX();
	
	var boundGetX = retriveX.bind(module);
	
	boundGetX();
```

### 偏函数
这个例子是使用bind函数用指定初始化参数创建一个函数。这些参数在this value后面，并被插入到绑定函数的实际传入参数的前面。

```js
  function list() {
     return Array.prototype.slice.call(arguments);
  }
 
  var list1 = list(1, 2, 3);
 
  var leadingThirtysevenList = list.bind(null, 37);

  var list2 = leadingThirtysevenList();

  var list3 = leadingThirtysevenList(1,2,3);
 
  console.log(list2);
  console.log(list3);
  
```

### setTimeout中的使用
在setTimeout中，this的指向已经不是new出的flower对象。使用bind函数，可以将this绑定到函数declare上，这样this的指向就没变化了。

```js
 function LateBloomer() {
     this.petalCount = Math.floor(Math.random() * 12) + 1;
 }

 LateBloomer.prototype.bloom = function () {
     setTimeout(this.declare.bind(this), 1000);
 };

 LateBloomer.prototype.declare = function() {
     console.log('I am a beatiful flower with ' +
                     this.petalCount + ' petals!')
 }

 var flower = new LateBloomer();
 flower.bloom();

```

###  用作构造器的绑定函数
绑定函数非常适用于通过new操作符使用目标函数构造新的实例的场景。当绑定函数被用于构造一个value， 原来的this是被忽略的,可以默认传入null。但是给定的参数还是前置在构造函数调用前。

```js
 function Point(x, y) {
     this.x = x;
     this.y = y;
 }

 Point.prototype.toString = function () {
     return this.x + ',' + this.y;
 };

 var p = new Point(1,2);
 p.toString();

 var YAxisPoint = Point.bind(null, 0);

 var emptyObj = {};
 var YAxisPoint = Point.bind(emptyObj, 0);

 var axisPoint = new YAxisPoint(5);
 axisPoint.toString();

 console.log(axisPoint instanceof Point);
 console.log(axisPoint instanceof YAxisPoint);
 console.log(new Point(17, 42) instanceof YAxisPoint);
```

### shortcuts

``` js
	var unboundSlice = Array.prototype.slice;
	var slice = Function.prototype.apply.bind(unboundSlice);
	
	slice(arguments);

```