---
title: Mysql 8.0 安装笔记
date: 2018-07-08 17:41:46
categories: Mysql
tags: Mysql
---

一、下载安装包

访问地址：https://dev.mysql.com/downloads/mysql/


![Alt text](http://soujava.com/images/mysqlDownload.png)

二、安装

 1、在D盘中新建mysql文件夹，然后把下载下来的安装包解压到里面。
 
```java

	D:\develop\soft>cd mysql
```
 
```java

    D:\develop\soft\mysql>dir
	
		D:\develop\soft\mysql 的目录
		
		2018/06/30  12:42    <DIR>          .
		2018/06/30  12:42    <DIR>          ..
		2018/06/30  14:19    <DIR>          mysql-8.0.11-winx64
		               0 个文件              0 字节
		               3 个目录 522,972,643,328 可用字节*
	
```

2、切换到mysql-8.0.11-winx64目录中

```java

	D:\develop\soft\mysql>cd mysql-8.0.11-winx64

	D:\develop\soft\mysql\mysql-8.0.11-winx64>dir
		 驱动器 D 中的卷是 新加卷
		 卷的序列号是 F6E6-5223
		
		 D:\develop\soft\mysql\mysql-8.0.11-winx64 的目录
		
		2018/06/30  14:19    <DIR>          .
		2018/06/30  14:19    <DIR>          ..
		2018/06/30  12:42    <DIR>          bin
		2018/06/30  14:59    <DIR>          data
		2018/06/30  12:42    <DIR>          docs
		2018/06/30  12:42    <DIR>          include
		2018/06/30  12:42    <DIR>          lib
		2018/04/08  14:44           301,518 LICENSE
		2018/06/30  14:01               855 my-default.ini
		2018/04/08  14:44               687 README
		2018/06/30  14:20                56 resetPWD.txt
		2018/06/30  12:42    <DIR>          share
		               4 个文件        303,116 字节
		               8 个目录 522,972,643,328 可用字节
```

3、列出mysql-8.0.11-winx64文件夹中的目录

```java
	
	D:\develop\soft\mysql\mysql-8.0.11-winx64>dir
		 驱动器 D 中的卷是 新加卷
		 卷的序列号是 F6E6-5223
		
		 D:\develop\soft\mysql\mysql-8.0.11-winx64 的目录
		
		2018/06/30  15:06    <DIR>          .
		2018/06/30  15:06    <DIR>          ..
		2018/04/08  16:53    <DIR>          bin
		2018/04/08  16:53    <DIR>          docs
		2018/04/08  16:53    <DIR>          include
		2018/04/08  16:53    <DIR>          lib
		2018/04/08  14:44           301,518 LICENSE
		2018/06/30  15:09               856 my.ini
		2018/04/08  14:44               687 README
		2018/04/08  16:53    <DIR>          share
		               3 个文件        303,061 字节
		               7 个目录 521,901,731,840 可用字节

```

4、在根目录中编辑my.ini配置文件(如果没有该文件则新建一个)

 *注：其中的basedir、datadir需要更改为你本地硬盘中的真实目录地址*

```java

	[mysqld]
	# 设置3306端口
	port = 3306
	# 设置mysql的安装目录
	basedir = D:\\develop\\soft\\mysql\\mysql-5.6.39-winx64
	# 设置mysql数据库的数据的存放目录
	datadir = D:\\develop\\soft\\mysql\\mysql-5.6.39-winx64\\data
	# 允许最大连接数
	max_connections=200
	# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
	max_connect_errors=10
	# 服务端使用的字符集默认为UTF8
	character-set-server=utf8
	# 创建新表时将使用的默认存储引擎
	default-storage-engine=INNODB
	# 默认使用“mysql_native_password”插件认证
	default_authentication_plugin=mysql_native_password
	
	[mysql]
	# 设置mysql客户端默认字符集
	default-character-set=utf8
	
	[client]
	# 设置mysql客户端连接服务端时默认使用的端口
	port=3306
	default-character-set=utf8

```
 
5、切换到bin目录中

```java

	D:\develop\soft\mysql\mysql-8.0.11-winx64>cd bin

```

5、初始化mysql配置信息

*注：在输出的命令行中，有mysql自动新建root账号时分配的随机密码，一定要记住这个密码，安装成功后需要登陆新建用户、数据库*

```java
	
	D:\develop\soft\mysql\mysql-8.0.11-winx64\bin>mysqld --initialize --console

	2018-06-30T07:10:18.607836Z 0 [System] [MY-013169] [Server] D:\develop\soft\mysql\mysql-8.0.11-winx64\bin\mysqld.exe (mysqld 8.0.11) initializing of server in progress as process 13940
	2018-06-30T07:10:18.613208Z 0 [ERROR] [MY-010340] [Server] Error message file 'D:\develop\soft\mysql\mysql-5.6.39-winx64\share\english\errmsg.sys' had only 886 error messages, but it should contain at least 4512 error messages. Check that the above file is the right version for this program!
	2018-06-30T07:10:25.877420Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: P6aiG/xZ,)7g 注意：这里就是mysql自动新建root账号时分配的随机密码，一定要记住这个密码，安装成功后需要登陆新建用户、数据库
	2018-06-30T07:10:29.016482Z 0 [System] [MY-013170] [Server] D:\develop\soft\mysql\mysql-8.0.11-winx64\bin\mysqld.exe (mysqld 8.0.11) initializing of server has completed

```


6、安装mysql服务

```java
	
	D:\develop\soft\mysql\mysql-8.0.11-winx64\bin>mysqld --install
	Service successfully installed.

```

7、启动mysql服务
	
```java

	D:\develop\soft\mysql\mysql-8.0.11-winx64\bin>net start mysql
	MySQL 服务正在启动 ..
	MySQL 服务已经启动成功。
	
```

8、登陆mysql

*登陆mysql时，提示输入的密码就是我们在第5步初始化mysql时，系统自动为我们生成的root密码*

```java

	D:\develop\soft\mysql\mysql-8.0.11-winx64\bin>mysql -u root -p
		Enter password: ************
		Welcome to the MySQL monitor.  Commands end with ; or \g.
		Your MySQL connection id is 8
		Server version: 8.0.11
		
		Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
		
		Oracle is a registered trademark of Oracle Corporation and/or its
		affiliates. Other names may be trademarks of their respective
		owners.
		
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.	
		

```

9、修改root密码

*因为mysql为我们随机分配的root登陆密码不好记忆，所以我们需要修改为我们方便记忆的密码*

```java
	
	mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'xteamsoft';
		Query OK, 0 rows affected (0.01 sec)

```

10、登陆成功后，可以显示目前的数据库列表

```java
	
	mysql> show databases;

	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| mysql              |
	| performance_schema |
	| sys                |
	+--------------------+
	4 rows in set (0.30 sec)

```

11、应用mysql数据库，然后显示mysql数据库中的所有表结构

```java

	mysql> use mysql;

	Database changed

	mysql> show tables;

	+---------------------------+
	| Tables_in_mysql           |
	+---------------------------+
	| columns_priv              |
	| component                 |
	| db                        |
	| default_roles             |
	| engine_cost               |
	| func                      |
	| general_log               |
	| global_grants             |
	| gtid_executed             |
	| help_category             |
	| help_keyword              |
	| help_relation             |
	| help_topic                |
	| innodb_index_stats        |
	| innodb_table_stats        |
	| password_history          |
	| plugin                    |
	| procs_priv                |
	| proxies_priv              |
	| role_edges                |
	| server_cost               |
	| servers                   |
	| slave_master_info         |
	| slave_relay_log_info      |
	| slave_worker_info         |
	| slow_log                  |
	| tables_priv               |
	| time_zone                 |
	| time_zone_leap_second     |
	| time_zone_name            |
	| time_zone_transition      |
	| time_zone_transition_type |
	| user                      |
	+---------------------------+
	33 rows in set (0.02 sec)

```

12、查询user表中的用户

```java
	
	mysql> select user,host,authentication_string from mysql.user;
	+------------------+-----------+-------------------------------------------+
	| user             | host      | authentication_string                     |
	+------------------+-----------+-------------------------------------------+
	| mysql.infoschema | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| mysql.session    | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| mysql.sys        | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| root             | localhost | *069686983B4218189367E02EAFDFC126EDAA7A2F |
	+------------------+-----------+-------------------------------------------+
	4 rows in set (0.00 sec)

```

13、创建我们自己的用户

```java

	mysql> CREATE USER 'maps'@'%' IDENTIFIED BY 'maps';
	Query OK, 0 rows affected (0.05 sec)
	
	mysql> select user,host,authentication_string from mysql.user;
	+------------------+-----------+-------------------------------------------+
	| user             | host      | authentication_string                     |
	+------------------+-----------+-------------------------------------------+
	| maps             | %         | *6D563D2A92CA78E4D2E08B5836BDDF5C4DC6ED04 |
	| mysql.infoschema | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| mysql.session    | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| mysql.sys        | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| root             | localhost | *069686983B4218189367E02EAFDFC126EDAA7A2F |
	+------------------+-----------+-------------------------------------------+
	5 rows in set (0.00 sec)

```

14、查看新建的maps用户的权限信息

```java

	mysql> show grants for maps;
	+----------------------------------+
	| Grants for maps@%                |
	+----------------------------------+
	| GRANT USAGE ON *.* TO `maps`@`%` |
	+----------------------------------+
	1 row in set (0.00 sec)
	
```

15、为maps用户授权(增、删、改、查)

```java

	mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON *.* to 'maps'@'%';
	Query OK, 0 rows affected (0.12 sec)
	
	mysql> show grants for maps;
	+--------------------------------------------------------------------------------+
	| Grants for maps@%                                                              |
	+--------------------------------------------------------------------------------+
	| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON *.* TO `maps`@`%` |
	+--------------------------------------------------------------------------------+
	1 row in set (0.00 sec)

```

*参考：https://dev.mysql.com/doc/refman/5.7/en/data-directory-initialization-mysqld.html*