---
title: Linux双网卡配置(内外网同时访问)
date: 2016-08-17 10:28:50
categories: linux
tags: Linux
---

事情的经过是这样的。。。。。；我有一台linux服务器操作系统，只能内网访问，现在需要提供给公司其他地域的同事通过VPN可以访问操作，那么就需要为这个linux操作系统配置一个新配置一个网卡，你修改网卡配置文件之前需要确保你的linux操作系统已经有两块物理网卡了，也就是说当你进入linux操作系统后，运行ifconfig命令可以看到eth0和eth1两个网卡信息，这才代表你的物理环境没有问题。

![](http://soujava.com/images/vSphere11.jpg)

**一、Ubuntu篇**

1、进入/etc/network目录中，编辑interfaces文件


![](http://soujava.com/images/linuxInter0.png)


2、配置eth1网卡的信息(ip地址，网关、子网掩码、DNS)信息
	
![](http://soujava.com/images/linuxInter1.png)

3、配置完相关信息后，需要编辑路由配置文件，配置域名服务器(这里配置完成后，永久生效)

	>vim /etc/resolvconf/resolv.conf.d/base

![](http://soujava.com/images/linuxInter2.png)

4、配置完网卡信息后，我们还不能连通外网，因为我们系统的路由默认采用内网网关进行通信的，我们需要把外网的网关设置成默认路由。先运行route命令查看路由信息，可以看到default 为eth0；即默认为内网网关。

![](http://soujava.com/images/linuxInter4.png)

5、把外网网关地址添加为默认路由，同时删除内网网关的路由

	>route add default  gw 192.168.1.1
	
	>route del default  gw 192.168.0.253

6、路由配置完成后，运行route命令我们可以看到default变成了eth1

![](http://soujava.com/images/linuxInter3.png)

7、重启网络服务

	>/etc/init.d/networking restart

8、总结：

- 查看物理环境十分正常，也就是通过ifconfig命令是否显示两块网卡设备
- 配置eth1网卡配置文件
- 添加外网dns地址
- 设置外网网关为默认路由
- 重启网络服务


注：我之前一直在/etc/resolv.conf中添加dns信息，后来才明白这个是临时文件，当在/etc/resolvconf/resolv.conf.d/base文件中配置完成后，/etc/resolv.conf中信息会自动生成的。


**一、Cent0S篇**

1、同样检查一下系统物理环境是否正常，通过ifconfig命令查看是否存在除eth0网卡设备外，还有其他网卡eth*;

![](http://soujava.com/images/vSphere11.jpg)

2、进入/etc/sysconfig/network-scripts/目录下面，我们可以看到有两个网卡设备的配置文件：ifcfg-eth0、ifcfg-eth1。

3、将ifcfg-eth0配置文件复制一份并改名为ifcfg-eth1。

	>cp ifcfg-eth0 ifcfg-eth1

4、修改ifcfg-eth1配置文件,具体信息如下
```java
	DEVICE=eth1
	TYPE=Ethernet
	NOBOOT=yes
	NM_CONTROLLED=yes
	BOOTPROTO=none
	IPADDR=192.168.*.*
	PREFIX=24
	GATEWAY=192.168.*.*
	DEFROUTE=yes	
	IPV4_FAILURE_FATAL=yes
	IPV6INIT=no
	NAME="System eth1"
	DNS1=*.*.*.*
	HWADDR=你的虚拟机的网卡设备的mac地址
	LAST_CONNECT=1465192940
	USERCTL=no	
```

5、重启网络服务(下面两条命令都可以)
	> service network restart
	> /etc/init.d/network restart