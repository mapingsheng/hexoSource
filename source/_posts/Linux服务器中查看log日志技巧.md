---
title: Linux服务器中查看log日志技巧
date: 2017-09-27 11:17:07
categories: Linux
tags: Linux
---
平时开发过程中，一般应用程序都会部署在linux操作系统上面，不管在什么阶段(开发、测试、线上)，难免会出现各种各样的问题，有些时候应该是bug，有些时候可能是偶然事件；不管出现什么问题，我们都需要找出问题所在，并且快速解决它；通常的做法无非是以下两种策略：

![Alt text](http://soujava.com/images/log0.jpg)

<!--more-->

- 再次复现问题，并实时监控日志，排查出问题
- 分析已有的日志数据，一步一步的分析排查问题

一般服务上面的日志文件都比较大，少则数GB；多则数TB；如果我们把一个数GB的日志文件放到window系统的电脑中进行分析，仅仅去打开这个日志文件，估计电脑就会卡爆，更别说全局搜索关键字啦。然而在Linux服务器上面直接通过一些命令去操作分析这个数GB的日志文件，那是轻而易举的事情，下面我们可以看到一个服务器中日志文件的大小是


![Alt text](http://soujava.com/images/log1.png)



一、**查看日志信息的相关命令**

1. tail -f catalina.out

	该命令可以实时查看新输出的增量log日志信息，所以适合查看访问量不大的应用服务，比如访问量很高，后台日志输出速度非常快，每秒千行的速度，用肉眼是根本无法看出什么日志的。

2. tail -n 1000 catalina.out

	查看日志文件中最后(最新)的1000行日志信息，这种方式适合查询‘复现问题操作时，查看该复现操作所产生的所有相关的日志信息’比较有用，比上面的tail -f 命令的好处就是可以快速查看最后操作的日志信息，有点快照的概念

3. tail -n +100 catalina.out
	
	从日志文件的第100行开始向后展示；这种方式适用于针对日志文件很大时的问题排查，跟上面的命令大同小异吧
	
4. sed -n '10,15p' catalina.out
	
	查看日志文件的第10~15行的日志信息，这种方式适合针对历史产生的日志信息进行分析排查，比如我们可以先大概定位到问题发生的日期区间，然后用sed命令把这个日期区间的所有相关日志信息进行输出排查。	
	
5. cat catalina.out
	
	该命令输出整个日志文件的所有日志信息，所以该命令适合查看日志文件比较小的情况下使用，所以该命令一般都会结合其他命令去使用。比如：

	>cat catalina.out | head -n 1000 | tail -n +500
	
	查看日志文件从1000行开始显示，查看500行的日志信息，即查看1000~1500行的日志数据。

6. grep

	如果想更加快速的排查分析日志文件中的日志数据，不会少了grep命令的使用，这么强大的搜索命令绝对可以让你排查问题的时候洋洋得意如沐春风。


二、**模拟现实场景进行问题排查**


>9月25日，收到反馈平台的系统通知，有用户反馈大概上午10点30分左右，IM功能模块无法正常使用，发送的消息都是失败状态。


- 问题分析步骤
	- 
>1.先大概找出9月25日 10点30分左右的日志数据

>>cat catalina.out | grep '2017-09-26 10:30'

![Alt text](http://soujava.com/images/log2.png)


>2.再次根据实际的情况，缩小数据查找分析范围，比如在查看10点30分产生的数据时，从第176行开始查看

>>cat catalina.out | grep '2017-09-26 10:30' | tail -n +176

![Alt text](http://soujava.com/images/log3.png)


通过以上两步不停的缩小范围，相信很快就可以定位到问题所在了。
 





