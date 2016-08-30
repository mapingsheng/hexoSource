---
title: CentOS-6.4内核升级操作
date: 2016-08-19 16:00:13
categories: Linux
tags: Linux
---
Linux下涉及到系统内核的一些软件，需要对内核升级才能使用，例如Docker的最低要求是2.6.32-431等等。
```java
	注意：
     如果在make menuconfig步骤报错，可尝试以下方法：
     1、修改scripts/kconfig/Makefile 文件
     2、找到HOSTLOADLIBES_mconf 在行末加入  -ltinfo
```
**一、先查看当前centos的系统内核**

![Alt text](http://soujava.com/images/linuxKenel1.png)

**二、下载内核包，如：linux-3.4.56.tar.xz**
	
```java
	https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.4.56.tar.xz
```

**三、安装内核**
```java
	# tar -Jxvf linux-3.4.56.tar.xz
	# mv linux-3.4.56 /usr/src/linux-3.4.56
	# cd linux-3.4.56
	# make mrproper #清除环境变量
	# make menuconfig 
	# make clean
	# make bzImage
	# make modules
	# make modules_install
	# make install
```

**四、编辑/etc/grub.conf文件，将default=1改成default=0**
	

**五、重启系统并检查内核**
