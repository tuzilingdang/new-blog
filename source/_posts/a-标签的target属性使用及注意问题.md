---
title: a标签的target属性使用及注意问题
date: 2017-09-05 23:11:43
tags: a标签 target
---

## 1. a标签的target的使用

一般书写a标签，我们常常用到的属性有href、target、id，以此实现点击a标签跳转新的文档或者文档的某个部分的功能。 

href属性用来描述跳转链接的地址，target用来定义被链接的文档在何处显示。target的值常见的有：
	
  值 | 描述
------|----
 view_window  | 文档重定向到单独的窗口
 name of frame   | <frameset>将超链接内容定向到框架中
 _blank | 新打开、未命名窗口中载入文档
 _self  | 没有指定目标的a标签的默认目标
 _parent  | 文档载入父窗口或者包含来超链接引用的框架的框架集
 _top  | 清除所有被包含的框架并将文档载入整个浏览器窗口
 
 
 接下来重点讲常用的_blank，看看有什么有意思的地方。
 
### 1.1 target="_blank"的使用
用Google搜索的时候，会发现英文站点击搜索结果，结果页是在当前窗口刷新的；但如果是使用百度，点击搜索结果，是在新页面中打开的。对于使用谷歌网速不佳的情况，自己还是不太习惯同一个窗口打开。_blank可以用在这样一些情况：

1. 打开外部链接
2. 打开PDF文件
3. 用户播放一些音乐、视频等内容
4. 用户当前页面有操作，改变会导致操作丢失

看起来用户体验还可以啊，也很方便啊，那就用用用起来吧。 但是， 真用起来问题还真是挺多啊，接下来看看有什么问题会被投诉吧。



## 2. target使用需注意的问题

这里主要讲讲target="_blank"带来的问题。

###  2.1 	 target="_blank"带来的问题

新窗口打开页面看似简单，但直接使用target="_blank"可能会出现如下情况： 

1. **兼容问题**： opener的锅，如果href是“”， 使用target="_blank"在Chrome中挺正常的，结果到了IE上就麻烦了，肿么打开了一个奇怪的空白页呀………………
2. **安全问题**： 又是opener的锅，谁让你可以跨域呢。之后就被钓鱼网站愉快的利用了。
2. **性能问题**： 还是opener的锅，opener怎么这么多锅…… 页面卡了啊………… 。


#### target="_blank"的兼容性

在chrome正常的打开原页面，IE是空白页。 如果用在列表上，后台传来的数据刚好href是空的，那这里就出bug了。
这里的原因是window.opener在chrome中自动设置成当前页面，然后新打开页面是当前页，所以整体观感还可以接受。 但似到了IE中，opener就直接打开aboutblank了，这个bug有点大。

#### target="_blank"的安全性

这里的问题就是被钓鱼……
当我们使用target="_blank"时，重定向页面使用window.open去打开，但是window.open这个对象是可以跨域的，也就是说我们可以随意修改js文件，在里面重新给window.open.location赋值，之后只要用户点击a标签，就会打开我们设置的链接。如果是个钓鱼网站，那就悲剧了。

可以试一下最简单的：

```html
	<script type="text/javascript">
		window.opener.location = "http://www.baidu.com";
	</script>
```

接下来注意了，开始钓鱼：

**1. 正常访问的页面，注意链接，注意链接**

a标签是这样写的：

```html
	<a href="./hack.html" target="_blank">Click</a>
```

![1](../../../../img/a-标签的target属性使用及注意问题/1.png)

**2. hack.html是a标签链接的页面，js的写法如下：**

```html
	<script type="text/javascript">
		var elements = document.getElementsByTagName('div');
		if(window.opener) {
			elements[0].innerHTML = "钓鱼成功了";
			opener.location = "./target-blank.html?money=10000";
		}
		else{
			elements[0].innerHTML = "钓鱼失败了";
		}
	</script>
```

**3. 然后钓鱼成功了，原页面的地址被改掉**

![1](../../../../img/a-标签的target属性使用及注意问题/2.png)


**接下来看看原页面的地址，关键是改掉了用户毫不知情啊毫不知情啊**：

![1](../../../../img/a-标签的target属性使用及注意问题/3.png)
假设上面的opener.location = "./target-blank.html?money=10000"前面改成请求后端的接口地址，这个ajax请求刚好又是Get请求，哈哈，那就悲剧了。 当然，现在一般后台都有各种限制，有数据提交的情况不会用get方式处理。

#### target="_blank"的性能问题

target="_blank"的性能问题在Chrome和Firefox会有涉及，IE没有这个问题。这里的问题原因是使用opener打开的新窗口和原有的窗口共用了一个进程。

但素，正常打开一个页面不应该是新建一个进程么，这是因为opener对象的包含了DOM信息，两个进程无法控制这样的DOM，所以放在一个进程里。

所以， 如果本身原有访问页面非常复杂，然后搞个钓鱼网站写个setInterval这样的函数，在原页面上点点点点点多几次a标签打开搞破坏网站的窗口，原页面就会卡死啦。

### 2.2 target="_blank"的优化方案

针对以上的问题，targe='_blank'还是慎用，如果出现上述问题，那就通过为 a标签 加上 rel=”noopener noreferrer” 属性来解决。

```html
<a href="" target="_blank"  rel="noopener noreferrer">
```









