---
title: JavaScript学习笔记-异步处理机制
date: 2017-09-21 22:39:48
tags: JavaScript学习笔记; 异步
---

## 例子
来看这样一个🌰栗子：

```js
async function async1(){
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}

async function async2() {
    console.log('async2 start');
}

console.log('script start');

async1();

setTimeout(function(){
    console.log('setTimeout');
},0);

new Promise(function(resolve){
    console.log('promise start');
    resolve();
}).then(function(){
    console.log('promise end');
});

console.log('script end');
```

思考程序输出的结果，没仔细想的话，结果可能是这样的：

```js
script start
async1 start
async2 start
async1 end
promise start
script end
setTimeout
promise end

```

再仔细想想，这次应该对了吧？

```js
script start
async1 start
async2 start
promise start
script end
async1 end
setTimeout
promise end

```

用node执行以下看看，居然还是不对呀，结果居然是这样的：

```js
script start
async1 start
async2 start
promise start
script end
async1 end
promise end
setTimeout
```

##  JavaScript的异步处理机制

### （1） JavaScript的单线程

作为浏览器中使用的语言，JavaScript一个最大的特点是单线程。 具体线程和进程的知识可以参考阮一峰大神的博客[进程与线程的一个简单解释](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)，非常形象的讲解了这两个概念。


这就说明在执行一段代码时，运行了一个进程，在这个进程上只能运行一个线程。所有任务的执行都需要排队进行。

### （2）事件循环

**神马是事件循环**

1. 事件循环用于浏览器管理事件、用户交互、scrpts、渲染、网络等。
2. 每个用户代理必须有且只有一个事件循环。
3. 一个事件循环必须至少有一个浏览器上下文，也就是一个document对象的的执行环境。浏览器上下文消失。事件循环也随之消失。
4. 事件循环可以有多个任务队列，一个任务队列中有一系列任务的顺序列表。这些任务包括以下内容：
   事件、回调函数、HTML解析、异步获取资源、DOM操作。
	

**实现方式类似这样**：

```js
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```
事件循环中每次执行完一个消息，才会开始执行下一个。比如，调用setTimeout时，传入两个参数，一个需要执行的回调函数，一个是时间段。这个时间段过后，将会在队列中添加一条信息。如果有其他消息需要处理，这时setTimeout的回调函数也需要等待其他消息处理完才能够执行。


事件循环还有一个重要的特性是不会阻塞。正由于异步的机制，比如等待一个ajax请求返回数据前，JavaScript还是可以同时处理其他的事情。


### （3）堆、栈和队列

#### 堆、栈和队列

下图是MDN Docs上对JavaScript的堆、栈和队列的描述：

![栈堆和队列](https://developer.mozilla.org/files/4617/default.svg)

**【堆】**

堆中的内存是未被分配大小的。 一段代码中的对象会被分配到堆中。

**【栈】**

这里的栈是JavaScript的调用栈，遵循FILO的原则。先被调用的函数会被先压栈，后被调用的函数再次压栈，执行完毕后，上层的函数先出栈，然后是下一个函数。

**【任务队列】**

JavaScript运行时会包含一个待处理的消息队列。每个消息会关联一个函数。



### （3）同步任务和异步任务

在JavaScript执行中，可以把任务分成两种，一种是同步任务，一种是异步任务。结合（1）和（2）中所述，我们可以归纳如下的过程：

（1）JavaScript是单线程的，这里的同步任务就全部放在主线程上执行，前一个任务执行完毕，才能执行下一个任务。

（2）异步任务会放入任务队列，只有主线程的同步任务执行完了才开始读取任务队列上的异步任务。

（3）每当任务被完成时（可以是一个IO的读取、ajax请求或用户事件等），任务队列中就会添加进来一个事件，下一步这个异步任务就被放入执行栈中。主线程开始处理执行栈中的函数。

（4）主线程循环处理第（1）（2）（3）个步骤。


##  例子的代码执行过程

### （1）压栈顺序

所有任务均按上述过程在主线程进行处理，整体压栈顺序及出栈顺序可见下图：

![压栈顺序图](../../../../img/JavaScript学习笔记-异步处理机制/1.png)

### (2) 任务队列

任务队列中放入的是异步任务，任务完成时，会添加进来一个事件。主线程执行完同步任务后开始执行异步任务。依次有以下几个任务：

1. console.log('async1 end');

2. promise.then(function(){
	console.log('promise end');
});

3. function() {
	console.log('setTimeout');
};


### (3) 为什么setTimeout的回调结果最后才显示？

可参见这篇博客：[从Promise来看JavaScript中的Event Loop、Tasks和Microtasks](http://www.tuicool.com/articles/MnY7N3a)。
博客中有这样一个例子：

```js
(function test() {
    setTimeout(function() {console.log(4)}, 0);
    new Promise(function executor(resolve) {
        console.log(1);
        for( var i=0 ; i<10000 ; i++ ) {
            i == 9999 && resolve();
        }
        console.log(2);
    }).then(function() {
        console.log(5);
    });
    console.log(3);
})()
```

正常思考后执行结果应该是 1 2 3 4 5， 但是执行的结果其实是1 2 3 5 4 。

接下来是对HTML5规范的翻译：

***Eventloop的处理模型***

1. 如果跟Documents有关的任务没有被完全激活，那就在Eventloop的任务队列中跑最早的任务。浏览器可以选择任意任务执行。
2. 如果Eventloop没有出现存储互斥，那就释放掉。（这个不懂……）
3. 第一步中的task如果已经被执行了，那就从任务队列中把它移除。
4. 如果Eventloop不是 worker's event loop， 那么执行以下三小步。

   * (1)  执行microtask检查
   * (2)  提供一个稳定的状态
   * (3) 如果必要的话更新Document或浏览器上下文的渲染和用户交互来反映当前状态。
5. 如果eventloop在全局作用域下执行，但在任务队列中没有事件，并且此时全局作用域对象中的closingFlag是true的话，那就销毁event loop。
6.  从第一步重复执行。

在这里promise的回校应该是被添加到了microtask队列中执行了，然后先输出5， 之后再执行下一个任务输出4。

看了半天，感觉还是理解的不清楚，之后有时间这部分内容会继续补充。