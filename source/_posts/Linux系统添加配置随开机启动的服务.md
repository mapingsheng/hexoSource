---
title: Linux系统添加配置随开机启动的服务-rc.local配置
date: 2016-07-25 17:46:24
categories: Linux
tags: 服务开机启动
---
在linux服务器操作系统中，经常会安装一些服务器端的软件如：tomcat、redis、MQ、Nginx等等常用的服务器组件；那么有时候服务器重启后需要用一个一个的启动命令把不同的软件启动起来；主要有以下几种场景：

1. 以root身份才能启动的服务
2. 以非root身份启动的服务(一般情况下都是以非root身份启动和管理服务)

**一、下面以把tomcat服务器配置成随开机自启动的配置为例说明一下**
```java
	tomcat的安装路径：/usr/local/server/apache-tomcat-8
```
那么我们知道了tomcat的安装路径后，就可以通过以下命令启动tomcat服务了
```java
	/usr/local/server/apache-tomcat-8/bin/start.sh
```
如果我们想让linux每次开机时自动启动tomcat服务，那么我们需要如下配置：
   
1. 打开 /etc/rc.d/rc.local文件，在该配置文件中添加tomcat的启动命令
```java
	touch /var/lock/subsys/local
	/usr/local/server/apache-tomcat-8/bin/start.sh
```
**二、下面以把redis服务配置成随开机自启动的配置为例说明一下（以redis用户启动）**
```java
	redis的安装路径：/usr/local/cache/redis-2.8.19
```
如果我们通过输入命令启动redis的方式是：(假设redis服务启动时必须用redis用户)
```java
	/usr/local/cache/redis-2.8.19/src/redis-server
```
如果我们想让linux每次开机时自动启动redis服务，那么我们需要如下配置：
1. 打开 /etc/rc.d/rc.local文件，在该配置文件中添加tomcat的启动命令
```java
	touch /var/lock/subsys/local
	su redis -c "/usr/local/cache/redis-2.8.19/src/redis-server"
```
**三、总结**
我们主要通过在rc.local文件中添加我们服务的启动脚本的方式来配置随开机自启动服务；当然对于linux操作系统来讲，并不是仅仅就这一种方式配置随机启动操作，其他方式后续再说。那么rc.local到底是什么文件呢？

rc.local其实是一个脚本文件，该脚本是在系统初始化级别脚本运行之后再执行的，因此可以在里面添加你想在系统启动之后执行的任何操作，比如启动第三方的服务，比如挂载mount操作等等。