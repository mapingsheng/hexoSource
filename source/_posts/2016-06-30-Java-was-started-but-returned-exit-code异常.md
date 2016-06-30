---
title: Java was started but returned exit code异常
date: 2016-06-30 15:56:30
categories: IDE
tags: eclipse
---
今天eclipse启动时一直报个错：Java was started but returned exit code=-805306369.........等等
将eclipse.ini内容改为：
（注：最下面的几行是调整后的配置）

-startup
plugins/org.eclipse.equinox.launcher_1.1.0.v20100507.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_64_1.1.0.v20100503
-product
org.eclipse.epp.package.jee.product
--launcher.defaultAction
openFile
--launcher.XXMaxPermSize
256M
-showsplash
org.eclipse.platform
-vmargs
-Dosgi.requiredJavaVersion=1.5

> -Xms1024m
-Xmx1024m
-XX:MaxPermSize=128m
-XX:+CMSClassUnloadingEnabled
-XX:+CMSPermGenSweepingEnabled
-XX:+UseParallelGC
-XX:CompileCommand=exclude,org/eclipse/core/internal/dtree/DataTreeNode,forwardDeltaWith
