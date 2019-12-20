---
title: 性能专题：（1）前端页面性能指标详解
date: 2019-07-04 19:16:40
tags:
---

# 常用性能指标
## 用户角度的RAIL性能模型
RAIL 是一种以用户为中心的性能模型,在衡量一个页面的性能好坏时，站在用户角度通常我们会关注以下几个方面，其关键性能指标可见下方表格：

<!--![](http://q0hfs054j.bkt.clouddn.com/img/rail.png)-->
![](https://img11.360buyimg.com/jdphoto/s1390x504_jfs/t1/93389/29/1773/17133/5dc531e2Ea85a9a71/6f68d3b1ab0bbefe.png)

> #### 关键RAIL 指标汇总

| RAIL模型  	   | 关键指标         | 用户操作        |
|:------------- |:---------------:| -------------:|
| 响应      | 	输入延迟时间（从点按到绘制）小于 100 毫秒 |  用户点按按钮（例如打开导航） |
| 动画      | 	每个帧的工作（从 JS 到绘制）完成时间小于 16 毫秒      | 用户滚动页面，拖动手指（例如，打开菜单）或看到动画。 拖动时，应用的响应与手指位置有关（例如，拉动刷新、滑动轮播）。 此指标仅适用于拖动的持续阶段，不适用于开始阶段 |
| 空闲      | 主线程 JS 工作分成不大于 50 毫秒的块       |  用户没有与页面交互，但主线程应足够用于处理下一个用户输入。 |
| 加载	      | 页面可以在 1000 毫秒内就绪       |    用户加载页面并看到关键路径内容 |

## Developer角度的性能指标

前端性能需要关注的指标可见下表：

| 名称  	   | 英文        | 含义       |
|:------------- |:---------------:| -------------:|
| FCP      | 	First Contentful Paint |  浏览器渲染DOM内容的第一个字节 |
| FMP      | 	Frist Meaningful Paint     | 首次有效渲染时间 |
| FID      | Frist Input Delay       |  用户实现交互操作的相应时间 |
| FCI | First CPU Idle | 首次CPU空闲时间 |
| TTI	      | Time to Interactive       |    页面开始加载到稳定可交互的时间 |
| Speed Index      | Speed Index       |  衡量页面内容在视觉上的填充速度 |
| FPS	      | Frames Per Second       |    每秒绘制的帧数 |
| 白屏时间	      | White Screen      |    统计起始点到页面出现第一个元素的时间 |
| 首屏时间	      | First Screen      |    页面首屏所有资源完全展示的时间|
| 内存占用空间     |  Heap	Size       |   内存占用空间|
| JS 堆内存大小     |  JS Heap	Size    |    JS 堆内存大小|
| DOM Nodes	      | DOM 节点数     |    DOM节点数量 |
| JS监听器数量	      | JS Event Listeners     |    JS监听器数量 |
| 代码覆盖率	      | coverage     |    代码覆盖率 |
| 关键文件大小	      | key file     |    关键文件大小 |

# 优化目标
| 指标  	   | 优化目标        |
|:------------- |:---------------:| 
| 首屏加载时间      | 	小于 1s | 
| FPS      | 	达到60 | 
| 每秒动画帧数      | 	达到60 |
| 填充速度指数      | 	小于1250 | 
| 关键文件大小 4G以下     | 	小于 345kb | 
| 关键文件大小  Wifi    | 	小于 750kb | 
| 关键CSS大小  | 	小于 15kb |
| 用户的响应时间  | 	小于 100ms |

# 性能指标统计方法及分析

在关注各项性能指标之前，有必要了解一下w3c标准的[Navigation Timing API](https://www.w3.org/TR/navigation-timing/) ，下图是level 2 标出的时间戳，level2 还处于Working Draft的阶段，即将进入Recommendation。Navigation Timing的属性对于目前我们的需要用到的性能指标有非常清晰的界定，可以提前了解一下。

<!--![](http://q0hfs054j.bkt.clouddn.com/img/timestamp-diagram_2019_11_08-11_10.svg)-->
![](https://wq.360buyimg.com/data/ppms/picture/timestamp_diagram_2019_11_08_11_10.svg)

Navigation Timing api 每个时间戳属性的定义可以查看下表：

| 属性  	   		|     含义     | 
|:------------- |:---------------:|
| redirectStart 	   		| 如果发生http重定向并且是同源的，这个属性将返回重定向开始的第一个请求发生的时间，否则返回0        |
| redirectEnd 	   		| 如果发生http重定向并且是同源的，这个属性将返回最后一次重定向接收完最后一个字节的时间戳，否则返回0        |
| fetchStart 	   		| 如果请求新的资源用的是GET请求方式，该属性返回的是用户代理检查任何应用缓存之前的时间，否则返回用户代理开始请求资源的时间       |
| domainLookupStart 	   		| 当前文档进行域名查找的开始时间，如果有持久链接或者从应用缓存或本地资源读取当前文档 ，返回的值就是fetchStart    |
| domainLookupEnd 	   		 | 当前文档进行域名查找结束的时间，如果有持久链接或者从应用缓存或本地资源读取当前文档 ，返回的值就是fetchStart    |
| connectStart 	   		 | 与服务器建立连接的开始时间，如果有持久链接或者从应用缓存或本地资源读取当前文档 ，返回的值就是domainLookupEnd    |
| connectEnd 	   		 | 与服务器建立连接的结束时间，如果有持久链接或者从应用缓存或本地资源读取当前文档 ，返回的值就是domainLookupEnd    |
| secureConnectionStart 	   		 | 可选属性，没有时返回undefined，采用https协议时则返回https连接开始的时间，否则为0   |
| requestStart 	   		 | 开始从服务器（或应用缓存或本地资源）请求当前实际文档的时间   |
| responseEnd 	   		 | 从服务器（或应用缓存或本地资源）接收到当前实际文档最后一个字节的时间   |
| domLoading 	   		 | 当前文档可读属性变成loading的时间   |
| domInteractive 	   		 | 当前文档可读属性变成interactive的时间点   |
| domContentLoadedEventStart 	   		 | DOM解析完后，DOMContentLoaded 事件开始相应前的时间点   |
| domContentLoadedEventEnd 	   		 | DOM解析完后，DOMContentLoaded 事件完成的时间点   |
| domComplete 	   		 | 当前文档可读属性变成complete的时间   |
| loadEventStart 	   		 | load事件开始执行的时间   |
| loadEventStart 	   		 | load事件执行结束的时间   |

按照上述的时间节点，我们可以计算如下的性能指标：

### 开始统计的起始点

如果加入网页重定向，unload页面的耗时，这个时间点从navigationStart开始，如果忽略前面的耗时，一般从fetchStart开始计算。

### FCP

页面文档返回第一个字节的时间，对于开发者来说这个指标非常重要，首字节的返回时间代表从后端拿到数据的整体相应耗时。

### 白屏时间
从统计起始点到页面出现第一个元素的时间，可以使用domLoading - fetchStart 或 domLoading - navigationStart。

如果采用Navigation Timing Level 2 的标准，则使用domInteractive - fetchStart 或 domInteractive - navigationStart。 

不兼容performance api 的情况，可以采用如下方案记录白屏时间：

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>测试白屏时间</title>
        <script>
            window.navigationStart = Date.now()
        </script>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link href="css/style.css" rel="stylesheet">
        <script>
            window.firstPaintTime = Date.now()

            // 不兼容performance api 的情况，可以手动记录window.navigationStart的时间点
            const whiteScreen = window.firstPaintTime - performance.timing.navigationStart || window.navigationStart
        </script>
    </head>
    <body>
        <div>Test</div>
    </body>
</html>

```

### 首屏时间
页面首屏所有资源完全展示的时间。从视觉角度来看非常容易观测，但是前端比较难确定这个时间点。可以采用下面几种方法来记录：

1.  performance API

performance.timing.domContentLoadedEventEnd - performance.timing.fetchStart。
	
2. 首屏加载最慢图片的加载完成时间

如果首屏图片比较多，一般加载最慢的是图片，所以电商类活动开发的页面中，图片多的情况通常可参照如下代码：

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>测试首屏加载时间</title>
        <script>
            window.navigationStart = Date.now()
        </script>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link href="css/style.css" rel="stylesheet">
        <script>
            window.firstPaintTime = Date.now()

            // 不兼容performance api 的情况，可以手动记录window.navigationStart的时间点
            const whiteScreen = window.firstPaintTime - performance.timing.navigationStart || window.navigationStart
        </script>
    </head>
    <body>
        <div>Test</div>
        <img src="http://e.hiphotos.baidu.com/image/h%3D300/sign=a9e671b9a551f3dedcb2bf64a4eff0ec/4610b912c8fcc3cef70d70409845d688d53f20f7.jpg" alt="img1" onload="onImgLoad()">
        <img src="http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg" alt="img2" onload="onImgLoad()">
        <img src="http://a.hiphotos.baidu.com/image/h%3D300/sign=a62e824376d98d1069d40a31113eb807/838ba61ea8d3fd1fc9c7b6853a4e251f94ca5f46.jpg" alt="img3" onload="onImgLoad()">

        <script>
            window.onload = function() {
            	   // 首屏加载时间
                window.firstScreen = window.lastImgLoadTime - performance.timing.navigationStart || window.navigationStart
            }

            function onImgLoad() {
                window.lastImgLoadTime = Date.now()
            }     
        </script>
    </body>
</html>

```

3. 根据代码执行顺序

	图片不多，但首屏逻辑较为复杂的情况下: 比如调用接口多且复杂耗时。 
	
	可以找到首屏最后加载出数据的js执行函数，手动记录该时间，再减去上述的navigationStart时间。
	

### FPS

上述几个指标对首次加载页面的体验影响非常直接，整个页面都稳定的后，用户滚动或拖动页面，展示动画的流畅度可以通过FPS来观查。 FPS表示的是每秒钟浏览器更新的图片帧数。按照上述的RAIL模型，每个帧的工作（从 JS 到绘制）完成时间小于 16 毫秒，1s内完成的帧数就是1000/16， 大概是60帧左右。低于30 FPS 以下的动画，就让人感觉到明显的卡顿和不适了。


### 速度指数：Speed Index

<!--![](http://q0hfs054j.bkt.clouddn.com/img/201912121631.png)-->
速度指数代表页面的填充速度，具体的计算过程如下：

以下图片内容来自 [WebPagetest文档](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index)，具体内容详见链接

下图中有A，B两个页面，可以看到在1~2s内的页面渲染情况：

![](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index/compare_progress.png?attredirects=0)

量化到页面绘制的完整度，下方折线图可以看到随着时间推移，A，B两个页面的填充百分比。
![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1472780188125/using-webpagetest/metrics/speed-index/chart-line-small.png)

转自 [WebPagetest文档](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index)

下面两幅图是A，B两个页面填充百分比的对比：
			
![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1472780189487/using-webpagetest/metrics/speed-index/chart-progress-a-small.png) ![](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index/chart-progress-b-small.png?attredirects=0)
			
转自 [WebPagetest文档](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index)

速度指数的计算公式如下：

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1472780188199/using-webpagetest/metrics/speed-index/speedindexformula.png)

了解了速度指数的计算方法，实际的页面性能分析时，像light house 及 WebPagetest 工具都可以统计到该项指标，通常性能比较好的情况，Speed Index 的值应小于1250。



### 内存

当我们在写代码时要时刻提醒自己：更多的内存 === 更好的性能。长时间运行的应用应该处于稳定的状态，内存理论上会围绕一个常量震动。整体页面的性能不好，会导致页面卡顿，白屏等现象。

#### 内存问题的现象

*  **页面性能随使用时间的延长越来越差**

	可能是内存泄露，用户浏览页面的时间越长，可能由于页面的错误导致占用的内存越来越多。

*  **页面的性能一直很差**
	  
	可能是内存膨胀，页面使用的内存比实际应该使用的要多。
	
*  **页面出现延迟或者经常卡顿暂停**

	可能由于垃圾回收过于频繁，浏览器进行垃圾回收时会造成脚本执行的阻塞。



