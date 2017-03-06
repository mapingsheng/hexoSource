---
title: CentOS系统安装nodeJs
date: 2017-02-09 13:11:02
categories: nodeJs
tags: nodeJs
---
主要介绍在Linux(CentOS)系统中，nodeJs的安装与环境配置，首先我们需要到nodeJs官网下载对应的tar安装包，然后使用命令进行安装和配置操作(一共有源代码编译安装和二进制包安装，两种安装方式都是可以的)


## 一、二进制包安装 ##

1 下载二进制包

`cd /usr/local/develop/soft  #切换到对应的安装目录中`

`wget https://nodejs.org/dist/v6.9.5/node-v6.9.5-linux-x64.tar.xz  #下载官方的nodeJs二进制安装包`


2 命令安装

>tar -xvf  node-v6.9.4-linux-x64.tar.xz	#解压下载的二进制安装包
	
>cd node-v6.9.4-linux-x64/ && ll    #切换到已经解压的安装包目录中，并且详细列出所有的文件

```java
	drwxrwxr-x. 2 maps maps    27 1月   6 04:50 bin
	-rw-rw-r--. 1 maps maps 55480 1月   6 04:50 CHANGELOG.md
	drwxrwxr-x. 3 maps maps    17 1月   6 04:50 include
	drwxrwxr-x. 3 maps maps    25 1月   6 04:50 lib
	-rw-rw-r--. 1 maps maps 60477 1月   6 04:50 LICENSE
	-rw-rw-r--. 1 maps maps 17440 1月   6 04:50 README.md
	drwxrwxr-x. 5 maps maps    42 1月   6 04:50 share
```

	*可以看到二进制安装包中包含bin/ 目录*

3 配置环境变量

>vim /etc/profile


```PHP
	export NODE_HOME=/usr/local/develop/soft/node-v6.9.4-linux-x64
	export PATH=$NODE_HOME/bin:$PATH
	export NODE_PATH=$NODE_HOME/lib/node_modules:$PATH
```

>source /etc/profile	

4 验证是否安装成功

>node -v


```PHP
v6.9.4
```

## 二、源代码编译安装 ##

1 下载二进制包

```PHP
	cd /usr/local/develop/soft  #切换到对应的安装目录中
	wget https://nodejs.org/dist/v6.9.5/node-v6.9.5.tar.gz  #下载官方的nodeJs二进制安装包	
```

2 命令安装

>tar -zxvf  node-v6.9.4-linux-x64.tar.gz	#解压下载的二进制安装包
	
>cd node-v6.9.4-linux-x64/    #切换到已经解压的安装包目录中

>./configure  #配置

>make

>make install

3 配置环境变量

	*参照二进制安装方式中的环境配置*

4 验证是否安装成功

	*参照二进制安装方式中的验证*

