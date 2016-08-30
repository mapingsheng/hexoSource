---
title: Redis--安装
date: 2016-08-19 17:21:43
categories: Redis
tags: Redis安装
---
**一、下载安装包**
  下载redis安装包，下载方式很多比如如下方式都可以：
    1)、到官网下载http://redis.io需要的版本
    2)、在linux系统中用命令下载 $ wget http://download.redis.io/releases/redis-2.8.17.tar.gz
    下载成功后得到redis-2.8.17.tar.gz安装包   
**二、安装**
    1)、用命令解压缩安装包
       $ tar xzf redis-2.8.17.tar.gz

    2)、进入解压后的文件中
         $ cd redis-2.8.17

    3)、编译安装
          $ make

  注：make完后 redis-2.8.17目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli 
**三、启动服务**
     1)、进入到redis安装目录src下面
          $ cd /usr/local/redis-2.8.17/src
     2)、启动服务操作，启动服务主要有两种方式：
           A：采用默认配置方式启动(不推荐)
                $ ./redis-server
           B：采用制定配置文件启动(推荐)
                 $ ./redis-server ../redis.conf
     注：redis.conf是一个默认的配置文件。我们可以根据需要使用自己的配置文件,修改redis.conf  配置文件修改 daemonize yes ；该配置为使redis启动后可以后台运行
 
**四、测试** 
   1)、进入安装目录，运行redis-cli
         $ ./redis-cli
         redis> set username maps
         redis>get username
         "maps" 
      