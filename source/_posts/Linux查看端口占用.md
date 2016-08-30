---
title: Linux查看端口占用
date: 2016-08-17 11:30:49
categories: Linux
tags: 网卡
---
在操作linux服务器的时候经常会碰到端口被占用的情况，那么如果更好的找到某个端口被什么占用呢。

一、分布操作

1. 先使用下面命令，查看当前环境的所有进程
	
	>netstat  -apn

2. 然后看某一个端口，后面对应的PID，然后通过kill  PID,把该端口停止

	>kill -9 pid号
	
二、快捷操作

 使用如下命令查看对应的端口是否被占用

	>netstat  -apn | grep  protNO

例如：netstat -apn | grep 8080  查看8080端口是否被占用，如果没占用，则显示被占用的进程；如果未被占用，则什么也不会输出。