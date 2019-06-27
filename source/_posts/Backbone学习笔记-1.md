---
title: Backbone学习笔记(1)
date: 2017-07-02 17:41:28
tags: Backbone
---

最近一两年，react和vue火的不要不要的，各种前端框架层出不穷，backbone似乎普遍使用的比较少。 因为项目上用到它，还是有研究一下的必要。 但是最主要的原因是，backbone只有两千多行，如果想要深入的钻研JavaScript，并且想了解深层的MVC框架机制的话，backbone是一个非常好的用来研究源码的库。

本系列的Backbone学习笔记主要目的是探讨Backbone的Model／View层和Router之间的协作机制，记录常用API。接下来一个系列是Backbone的源码解读，将深入了解其实现原理。

好啦，进入正题，来看看backbone到底是怎么工作的吧。 接下来的几篇学习笔记，都会围绕下面TodoList的小例子的实现来讲解。具体源码可参考官方文档：[Demo of Todos List](http://daringfireball.net/projects/markdown/syntax).

## Backbone做了什么
Backbone最最重要的工作是把业务逻辑和用户界面分离开来。

## Backbone的核心构成

严格来讲，Backbone不是一个MVC框架。之所以这么讲，是因为在backbone中，model层和view层都比较清晰，但controller在这里体现的并不明显。与之作用类似的是Router这个东东，具体的用法会在笔记中记录下来。
最重要的Model和View， 功能如下：

### Model和View

描述清晰的官方文档图1：
![Model and View](http://backbonejs.org/docs/images/intro-model-view.svg)

##### Model做了什么
* 协调数据和业务逻辑
* 从服务端加载和保存数据
* 数据变化时发出事件

##### View做了什么
* 监听UI变化并渲染UI
* 处理用户输入和交互事件
* 将捕获的输入数据传给Model

### Collections

描述清晰的官方文档图2：
![Collections](http://backbonejs.org/docs/images/intro-collections.svg)

##### Collecions做了什么
* 处理一组相关的Model
* 加载和保存新的Model到服务器
* 为Model列表的聚合和计算提供帮助函数
* 代理模型内发生的事件，可以在一处监听可能发生在Collections内任意一个Model的变化

### Router
描述清晰的官方文档图3：

![Routers](http://backbonejs.org/docs/images/intro-routing.svg)

Router会监测到URL的变化，根据#号后的值来定位页面的地址。


### Events
Backbone的事件模块可以存在在任何对象中。对象可以绑定和触发自定义的命名事件，并且在绑定事件之前不需要声明。


接下来将以Todos的demo讲一下具体的实现。
