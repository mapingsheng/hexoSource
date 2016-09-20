---
title: RocketMQ启动报错解决
date: 2016-09-20 10:21:53
categories: RocketMQ
tags: RocketMQ启动错误
---
最近需要用到消息服务器，就用到淘宝开源的RocketMQ消息服务器了，重新安装的一台新的linux服务器（CentOS6.4），然后安装的RocketMQ二进制包。安装成功后，在最后启动的时候，报了一个错误！

>![Alt text](http://soujava.com/images/rocketMQ1.png)

注：根据上面的错误提示信息可以看到是因为刚刚安装的Centos操作系统的主机名称没有配置导致启动MQ服务时住不到主机

### 解决方法

1、vim /etc/hosts   
	
添加如下主机名配置信息

>![Alt text](http://soujava.com/images/rocketMQ2.png)

2、service network stop  

关闭网卡

3、service network start  

启动网卡

4、再重新执行MQ的启动服务命令后，可以看到下面的提示信息代表服务已经启动成功

>![Alt text](http://soujava.com/images/rocketMQ3.png)

