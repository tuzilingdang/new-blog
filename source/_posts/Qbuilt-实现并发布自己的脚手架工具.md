---
title: Qbuilt-实现并发布自己的脚手架工具(1)
date: 2017-03-31 22:05:16
tags: 脚手架工具
---

常用的脚手架工具有yoman等，现在比较火的react开发网上也有create-react-app等等可以直接安装过来使用。但是平时自己做项目会根据不同的框架、类库等等选择不同的目录结构。如果能有一个工具把自己的不同的项目目录作为常用模板添加进去，之后就可以各种愉快的选择不同的模板并下载到本地使用了，再也不用每次一个个文件夹、package.json等的添加了，生成新项目目录效率高高的。直接生成一个项目目录到本地，npm install一下就可以搞定所有需要的模块了。

最近几天终于实现了一个前端脚手架工具，已经发布到npm上，大家可以下载过来直接使用了。 Qbuilt本来叫Qbuild，Quick Build 的缩写，不过被占用了。 本篇先上使用教程，看看有哪些小功能。

# Qbuilt
A tool for scaffolding frontend project.

## Start 

### 1. 安装| Install 

`` npm install qbuilt ``

### 2. 查看现有模板| Look up existed templates

`` qbuilt list`` 

or you can input this simple command `` qbuilt l ``

![43](/img/Qbuilt-实现并发布自己的脚手架工具/list.png)

### 3. 添加自己的模板| Add your own template

If you have a template which often used in project, first you can upload this template to github , then add it to qbuilt.

Sign in github and create a new repository, and push local template to remote repository.

Input command `` qbuilt add `` to create your own template , and clone the git url of your template to it. Then , after the next step the template will be used in your new project.

![43](/img/Qbuilt-实现并发布自己的脚手架工具/add.png)

### 4. 初始化项目目录| Initialize project directory

`` qbuilt init`` to initialize a project , `` qbuilt i `` for short. 

cd filepath  , and use ``npm install ``to install node modules needed in the project.
![43](/img/Qbuilt-实现并发布自己的脚手架工具/init.png)

### 5. 删除模板 | Delete template

`` qbuilt delete`` to delete template you have added to qbuilt, use `` qbuilt d`` for short.
![43](/img/Qbuilt-实现并发布自己的脚手架工具/del.png)


本篇已经详细的介绍了Qbuilt具体的使用，下一篇的重点就看看具体要怎么实现，敬请期待。