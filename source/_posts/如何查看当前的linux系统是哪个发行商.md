---
title: 如何查看当前的linux系统是哪个发行商
date: 2016-08-03 16:06:33
categories: linux
tags: linux
---
我们刚开始进入一个linux操作系统，可能只知道它是一个操作系统，但是不太清楚到底是那个发行商，比如ubuntu、Centos、Debian等等；那么当然老鸟通过查看某些目录的配置文件一眼就可以看到是什么发行商；对于新鸟来说有以下几个技巧可以查看：

一、 通用方法-适用于当前市面上绝大多数linux

  **lsb_release -a**
```java
	[maps@iZ25017n0ruZ conf]$ lsb_release -a
	LSB Version:	:core-4.1-amd64:core-4.1-noarch
	Distributor ID:	CentOS
	Description:	CentOS Linux release 7.0.1406 (Core) 
	Release:	7.0.1406
	Codename:	Core
```
输入以上命令后可以看到该linux操作系统是CentOs,版本是7.0.1406

二、 非通用-技巧

1. 首先通过cat /proc/version命令查看当前linux系统内核信息
```java
	[maps@iZ25017n0ruZ etc]$ cat /proc/version 
	Linux version 3.10.0-123.9.3.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.2 20140120 (Red Hat 4.8.2-16) (GCC) ) #1 SMP Thu Nov 6 15:06:03 UTC 2014
```
通过上面命令输出信息我们可以看到系统为Red Hat 4.8.2系列
```java
	[maps@iZ25017n0ruZ etc]$ cat /proc/version 
	Linux version 3.13.0-32-generic (buildd@kissel) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #57-Ubuntu SMP Tue Jul 15 03:51:08 UTC 2014
```
通过上面输出信息我们可以看到系统为Ubuntu系列

2. 然后切换到/etc/目录下面，看看是否os.release文件，然后通过"cat os.release"命令查看该文件内容信息
```java
	[**cat /etc/redhat-release**@iZ25017n0ruZ etc]$ cat ./os-release 
	NAME="CentOS Linux"
	VERSION="7 (Core)"
	ID="centos"
	ID_LIKE="rhel fedora"
	VERSION_ID="7"
	PRETTY_NAME="CentOS Linux 7 (Core)"
	ANSI_COLOR="0;31"
	CPE_NAME="cpe:/o:centos:centos:7"
	HOME_URL="https://www.centos.org/"
	BUG_REPORT_URL="https://bugs.centos.org/"
```
通过上面的输出信息我们可以确定该系统为CentOs 7

注：如果是redhat系列的linux系统，在/etc目录下面会有redhat-release文件，我们通过**cat /etc/redhat-release**命令也可以查看系统发行商信息
```java
	[maps@iZ25017n0ruZ conf]$ cat /etc/redhat-release
	CentOS Linux release 7.0.1406 (Core) 
```
