---
title: 运用webGL实现3D效果-机器组装
date: 2018-02-02 16:27:07
categories: 3D
tags: webGL
---

在二次元的世界里，如果有些东西能以3D的效果展示，给人的感觉是完全不一样的，比如我们平时使用的地图导航上面显示的3D画面、教学场景中使用的几何教学演示、一些3D场景的动画等等，针对这些应用，完全可以使用webGL实现，并且在浏览器中无障碍展示，并且还可以添加交互功能。

![Alt text](http://soujava.com/images/webgl1.png)

<!--more-->

由于3D是通过浏览器查看的，并且对应的数据源可以是多种格式（json、xml），说白了就是我们创建了一个静态的web网站，然后浏览网页中的3D场景而已；所以我们需要把对应的html页面部署到对应的web服务器中运行；大概的步骤如下：

**一、部署步骤**

>1.：安装nodejs服务

参考：[CentOS系统安装nodeJs](http://soujava.com/CentOS%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85nodeJs/)

>2.：部署webGL示例网页到nodejs服务中


- 新建node_server/publish文件夹
- 新建nodejs服务脚本文件server.js

```java

	var http = require('http');
	var express = require('express');
	var app = express();

	app.use(express.static("publish")).listen(8080);//创建http服务，访问publish文件夹中的资源，并监听8080端口，等同于启动了tomcat服务

```

*通过上面两步，我们就成功搭建了一个web服务器！*

- 把对应的3D示例相关web页面资源copy到publish目录中即可

 源码地址：[github.com/mapingsheng	](https://github.com/mapingsheng/webGL/tree/master/node_server_demo)

- 运行*node server.js* 启动服务，然后打开浏览器浏览效果

![Alt text](http://soujava.com/images/webgl2.png)

![Alt text](http://soujava.com/images/webgl3.png)

![Alt text](http://soujava.com/images/webgl4.png)


>3.：下面看一下3D场景的动画展示

![Alt text](http://soujava.com/images/webgl5.gif)

![Alt text](http://soujava.com/images/webgl6.gif)