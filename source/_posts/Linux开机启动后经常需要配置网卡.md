---
title: Linux开机启动后经常需要配置网卡
date: 2016-07-25 17:27:42
categories: Linux
tags: 网卡
---
在虚拟机中安装的CentOS6操作系统，每次开机启动后，网卡默认都是关闭的；所以系统对外网络是处于中断的状态的，所以也就不能通过Xshell工具远程连接操作。

![Alt text](http://soujava.com/images/wangka.jpg)

然后针对上面的情况，需要手动操作一下，把eth0网卡开启使用才行；感觉很繁琐！

![Alt text](http://soujava.com/images/wankapeizhi.jpg)

解决方法：
使用如下命令编辑网卡信息：
``` java
   vim /etc/sysconfig/network-scripts/ifcfg-eth0
```
网卡信息的内容大致如下：
```
	DEVICE="eth0"
	TYPE=ethernet
	UUID=1221231K-4DFSD-8SDFA
	ONBOOT="yes"
	IPADDR=192.168.1.152
	NETMASK=255.255.255.0
	GATEWAY=192.168.1.1
	BOOTPROTO=static
```
然后在网卡信息中找到"NOBOOT=no"配置选项，改成"NOBOOT=yes"

再重启电脑后就不需要再次手动配置激活网卡了！