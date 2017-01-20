---
title: Linux查看端口占用
date: 2016-08-17 11:30:49
categories: Linux
tags: 网卡
---
在操作linux服务器的时候经常会碰到端口被占用的情况，那么如果更好的找到某个端口被什么占用呢。

一、**分步操作**

1. 先使用下面命令，查看当前环境的所有进程
	
> netstat  -apn

2. 然后看某一个端口，后面对应的PID，然后通过kill  PID,把该端口停止

> kill -9 pid号
	
二、**快捷操作**

- 查看对应的端口是否被占用

> netstat  -apn | grep  protNO（端口号）

例如：netstat -apn | grep 8983  查看8983端口是否被占用，如果没占用，则显示被占用的进程；如果未被占用，则什么也不会输出。

>[maps@localhost bin]$ netstat -apn|grep 8983
    (Not all processes could be identified, non-owned process info
    will not be shown, you would have to be root to see it all.)
    tcp6       0      0 :::8983                 :::*                    LISTEN      3272/java 
    
  我们可以看到占用8983端口的进程id号为:3272
 
- 查看端口对应的进程ID信息
  
> ps -aux|grep 3272

------

>	[maps@localhost bin]$ ps -aux|grep 3272
    maps      3272  0.4 11.3 2596728 173412 pts/0  Sl   22:04   0:03 java -server -Xms512m -Xmx512m -XX:NewRatio=3 -XX:SurvivorRatio=4 -XX:TargetSurvivorRatio=90 -XX:MaxTenuringThreshold=8 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:ConcGCThreads=4 -XX:ParallelGCThreads=4 -XX:+CMSScavengeBeforeRemark -XX:PretenureSizeThreshold=64m -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=50 -XX:CMSMaxAbortablePrecleanTime=6000 -XX:+CMSParallelRemarkEnabled -XX:+ParallelRefProcEnabled -XX:-OmitStackTraceInFastThrow -verbose:gc -XX:+PrintHeapAtGC -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCTimeStamps -XX:+PrintTenuringDistribution -XX:+PrintGCApplicationStoppedTime -Xloggc:/usr/local/develop/soft/solr-6.3.0/server/logs/solr_gc.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=9 -XX:GCLogFileSize=20M -Dsolr.log.dir=/usr/local/develop/soft/solr-6.3.0/server/logs -Djetty.port=8983 -DSTOP.PORT=7983 -DSTOP.KEY=solrrocks -Duser.timezone=UTC -Djetty.home=/usr/local/develop/soft/solr-6.3.0/server -Dsolr.solr.home=/usr/local/develop/soft/solr-6.3.0/server/solr -Dsolr.install.dir=/usr/local/develop/soft/solr-6.3.0 -Xss256k -Dsolr.log.muteconsole -XX:OnOutOfMemoryError=/usr/local/develop/soft/solr-6.3.0/bin/oom_solr.sh 8983 /usr/local/develop/soft/solr-6.3.0/server/logs -jar start.jar --module=http
    maps     30521  0.0  0.0 112660   964 pts/0    R+   22:15   0:00 grep --color=auto 3272