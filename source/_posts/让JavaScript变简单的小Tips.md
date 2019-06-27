---
title: 让JavaScript变简单的小Tips
date: 2017-06-26 01:15:25
tags: JavaScript,ES6,Tips
---


平时写代码，看到一堆一堆的js代码经常眼花缭乱的，其实有许多写法上的小技巧可以让代码变得更加整洁易读。 记录一下这些很有用的小tips， 让写代码的效率变得高高的 ^_^

### 1. 变量判断赋值

长长的写法：

```
if(var1 !== null || var1 !== undefined || var1 !== '' ) {
	var var2 = var1;
}

```

简便写法：

```
var var2 = var1 || 'new';
```

### 2. for循环
####(1) 循环的简单写法

长长的写法：

```
var arr = [1,1,1,3,4,5];
for(let i = 0; i < arr.length; i++){
	console.log(arr[i]);
}

```


简便写法：

```
 var arr = [1,1,1,3,4,5];
 for(let index in arr){
 	console.log(arr[index]);
 }

```

or 另一种简单的写法：

```
var arr = [1,1,1,3,4,5];
arr.forEach(function(element){
	console.log(element);
});

```
####(2) 循环中带了太多个0

比如：

```
for (let i = 0; i < 10000000000; i++){}
```

怎么样，数不清了吧……
可以这样写：

```
for (let i = 0; i < 1e10; i ++) {}
```


### 3. ES6中的一些简单写法
（1）箭头函数

一条语句的写法：

```
// 一条语句
function sayName(name){
	return name;
}

// 简便写法
sayName = (name) => name;

```
多条语句写法：

```
// 多条语句
function add(arr) {
	var result = 0;
	for (let i = 0; i ++; i < arr.length) {
		result = result + arr[i];
	}
	return result;
}

// ES6写法
var add = (arr) => {
	var result = 0;
	arr.forEach((item) => {
		result = result + item;
	});
	return result;
}

// sort函数
var result = arr.sort(function(a,b){
	return a-b;
});

// ES6写法：
var result = arr.sort( (a,b) => a-b);

```
	

（2）解构赋值

```
// 原写法
var a1 = 3;
var a2 = 2;
var a3 = 1;

// ES6写法
var [a, b, c] = [1, 2, 3];

// 作用于对象
var person = {
	name: 'Amy',
	age: 18
};
var {name, age} = person;
console.log(name); // 'Amy'
console.log(age); // 18

// 作用于字符串
var [a,b,c] = 'cat';
console.log(a); // 'c'
console.log(b); // 'a'
console.log(b); // 't'
```

（3）默认参数值

```
// 	麻烦的写法
function sayHi(name, age) {
	if(name === undefined) {
		name = 'Amy';
	}
	if(age === undefined) {
		age = 18;
	}
	console.log('Hello,My name is' + name + ', I\'m ' + age + ' year\'s old!');
}

// 简单的写法
var sayHi = (name = 'Amy', age = 18) => (
		console.log('Hello,My name is' + name + ', I\'m ' + age + ' year\'s old!');
);
```

已经1点多了，只能先整理到这里，后面有时间再补充啦。
