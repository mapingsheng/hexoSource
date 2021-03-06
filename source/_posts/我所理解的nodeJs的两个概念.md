---
title: 我所理解的nodeJs的两个概念
date: 2017-03-06 16:22:02
categories: nodeJs
tags: nodeJs
---
仔细想了想，现将我对nodejs理解中的两个概念综述一下，以确定后续对其使用定位和深入理解。

## 1、包管理器 ##

其实nodejs有一个强大的包管理器支撑着nodejs的繁荣生态，提到这个包管理器就据下面几个例子：

<!--more-->

*用过linux操作系统的都知道：*

 如果用的是Debian、Ubuntu操作系统的话，那么我们平时安装软件时会用到【*apt-get install 包名称*】去安装；

 如果用的是Fedora/Redhat、CentOS操作系统的话，那么我们平时安装软件时会用到【*yum install 包名称*】去安装；

 再举一个例子，就是平时开发中用maven管理依赖；一般如果工程依赖什么jar包，然后在pom.xml中配置这个依赖的jar包信息就行了；

 通过上面三个例子是不是发现我们安装软件、添加依赖的jar包等操作时非常方便；可是有没有想到他们如果操作的，其实在后面有一个仓库；
	
 安装软件时有一个在线软件仓库，添加jar依赖时，有一个jar包仓库(本地或远程)；当执行安装命令时，会直接从仓库中把相关软件下载下来并且自动安装，就是这么爽；
 其实nodejs也有类似这么一个仓库(node package manage)，简称NPM

 如果安装nodejs仓库中的软件(模块)，可以通过npm命令【*npm install 名称*】；当然npm命令其实在我们安装nodejs时已经自动安装了，
 我们可以输入*npm -v*查看已经安装的npm的版本信息；当然如果没有安装npm的话，
 可以通过命令以下进行安装：
 >curl http://npmjs.org/install.sh | sudo sh  #如果非root用户需要添加sudo sh获取管理员权限

## 2、异步事件 ##

nodejs是基于事件机制进行处理的，不像多线程那样；拿一个例子来讲，比如在线购物时，"提交订单"的操作：
>如果用多线程方式：
>
>a、启动一个线程判断库存，并且减库存操作(花费200ms)
>
>b、启动一个线程修改订单状态操作(花费300ms)
>
>c、启动一个线程修改购物车中商品状态操作(花费400ms)
 
  当然上述流程只是假设，如果针对上面三个操作不使用多线程的情况，花费的时间就是三个操作的时间总和(花费900ms)；而如果采用多线程并行处理三个操作的话，那么花费的时间仅仅是三个操作中花费时间最长的一个操作的时间(花费400ms)。

>如果用nodejs方式：
>
>a、添加一个库存操作事件(花费50ms)
>
>>a.1、判断库存(花费100ms)
>
>>a.2、减库存操作(花费100ms)
>
>b、添加一个修改订单的事件(花费40ms)
				
>>b.1、修改订单状态操作(花费100ms)
>
>c、添加一个修改购物车的事件(花费50ms)
>
>>c.1、修改购物车中商品状态操作(花费400ms)

*注：上面的添加一个XX事件其实是将一个事件添加到事件管理器中，然后nodejs会自动且不间断的消费处理事件管理器中的事件。*

通过看到nodejs的操作步骤场景我们发现一个重要的特征就是不停的“添加一个XX事件”；
所以提交订单的操作时间将是(50ms+40ms+50ms=140ms)，相比多线程场景花费的时间(400ms)是不是少了很多；

>但是有一点我们需要注意:
>>1、使用nodejs的场景花费 140ms，并不是提交一个订单花费了140ms，而是用户提交一个订单收到的响应是140ms；
>
>>2、多线程场景虽然花费了400ms，但是提交订单这个操作全部完成了；而nodejs场景花费了140ms，但是提交订单这个操作还没有全部完成；这就是异步的概念
>
>>3、所以针对提交订单这个操作使用nodejs去处理貌似有点欠缺，因为提交订单操作涉及大量的“计算”，如果采用异步去处理，可能无法保证数据原子性和一致性。
所以nodejs的使用场景更适合密集型IO，比如应对大流量的请求、磁盘io等等。
