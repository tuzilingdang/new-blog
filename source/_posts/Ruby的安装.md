---
title: Ruby的安装
date: 2017-01-08 16:21:48
tags: ruby
---

## 1. RVM安装 ##

1. 执行命令  curl -L https://get.rvm.io | bash -s stable g   ,过程有点点慢，稍等一下就安装好了

2. 载入 RVM 环境，source ~/.rvm/scripts/rvm

3. 这下就安装成功啦，用 rvm -v 查看一下版本号

## 2. 用 RVM 安装 Ruby 环境 ##

1. 首先来看看ruby有哪些版本，执行命令 rvm list known
![43](/img/Ruby的安装/44.png)

2. 可以安装啦 ，新手可以找一个稳定的版本下载 rvm install 2.3.3，好漫长

3.  查询现在安装的版本 rvm list

## 3. 设置Ruby版本 ##

1. rvm 2.0.0 --default ，可设置为系统默认版本

2.   Test，  执行ruby -v
3.  访问ruby网址如果有问题，可以使用淘宝的网址 
	  $gem source -r https://rubygems.org/
	  $ gem source -a https://ruby.taobao.org
	  
	看看输出结果：$ gem sources -l

![43](/img/Ruby的安装/43.png)
