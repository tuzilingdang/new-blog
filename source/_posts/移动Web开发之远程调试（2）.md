---
title: 移动Web开发之远程真机、模拟调试（2）
date: 2017-02-26 18:36:53
tags: 真机 微信 远程调试 
---

在移动端web进行真机调试时，常常必须对iPhone进行调试。 上一篇已经讲到了如何利用safari进行开发调试，但是safari的开发者工具很多人用的不熟练。 那不如直接将Chrome进行到底，哈哈哈。 

这就有点麻烦，手机上打开safari浏览器，chrome无论如何也没有显示啊。这就要用到这个神器， ios-webkit-debug-proxy。

### 安装

安装途径1 ：最快捷的方式是用homebrew进行安装，一步到手呀。 用Mac装这个还是快不少的。

`` 
	brew install ios-webkit-debug-proxy
``

安装途径2： 

	sudo apt-get install autoconf automake libusb-dev libusb-1.0-0-dev libplist-dev libplist++-dev usbmuxd libtool libimobiledevice-dev

	git clone https://github.com/google/ios-webkit-debug-proxy.git
	cd ios-webkit-debug-proxy

	./autogen.sh
	make
	sudo make install
	
安装完这样
	
![43](/img/移动Web开发之远程调试（2）/1.png)

### iOS设备设置

木有用过iOS的模拟器，木有装Xcode。 真机上如上一篇的调试方法所述，拿起手机，进入设置 > Safari > 高级 > Web 检查器， 开。


### 开启代理

#### 第一步

`` 
	sudo ios-webkit-debug-proxy	
``

有错误…………

![43](/img/移动Web开发之远程调试（2）/2.png)

看看Mac电脑到底有木有检测到手机设备：

用这个命令:

`` 
	idevice_id -l	
``

![43](/img/移动Web开发之远程调试（2）/3.png)

显示确实有设备呀，可是就是显示不出来。仔细看看说明文档，对于上述提示，可以这样解决:

	brew update
	brew reinstall --HEAD libimobiledevice
	brew reinstall -s ios-webkit-debug-proxy

重新装完后，显示正常了。

![43](/img/移动Web开发之远程调试（2）/4.png)

如果还是持续有问题， 拔掉USB线， 重新插入， iPhone会显示信任该电脑的提示，选择信任。不信任的情况是无法正常连接成功的。

#### 第二步

输入 localhost:9221 ， 看到页面：

![43](/img/移动Web开发之远程调试（2）/5.png)

每个设备对应的端口号从9222开始， 9222-9322 ，到9322. 可以通过这个命令为不同的设 备指定一个唯一的端口号。
``
	ios_webkit_debug_proxy -c 设备号:9227		
``

点击页面的设备列表，就可以查看的当前设备上开启了哪些网页了。


![43](/img/移动Web开发之远程调试（2）/6.png)

``
sudo ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html
``

跟据端口号，改变page的页码可以看到对应的页面了。

chrome-devtools://devtools/bundled/inspector.html?ws=localhost:9222/devtools/page/1


![43](/img/移动Web开发之远程调试（2）/7.png)


这样，以后可以愉快的打开iPhone的safari浏览器，在电脑的Chrome上方便地进行调试了。 





