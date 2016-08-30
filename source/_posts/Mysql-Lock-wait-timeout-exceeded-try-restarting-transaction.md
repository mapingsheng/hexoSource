---
title: Mysql--Lock wait timeout exceeded try restarting transaction
date: 2016-08-19 16:46:36
categories: Mysql
tags: Mysql
---
一个业务正在操作mysql过程中，突然报提示错误信息：Lock wait timeout exceeded; try restarting transaction。
查询发现问题原因是，在执行操作的表被另外一个操作锁定，当前操作等待另外一个操作完成时提示超时。

**一、临时解决办法**

1.  执行以下脚本登录mysql：mysql -h 192.168.0.166 -u user -ppassword
2.	执行select * from information_schema.innodb_trx\G;查询当前处于锁等待的进程。查询结果只需要关注trx_mysql_thread_id所对应的值。
3.	执行show processlist;查询当前的所有进程，找到对应的ID，执行kill命令，kill id。

**二、Mysql数据库设置**

1.	设置innodb_lock_wait_timeout参数。通过show variables like 'innodb_lock_wait_timeout';查看当前设置的值。
2.	执行set global innodb_lock_wait_timeout=120;设置超时时间。
3.	设置是否自动提交事务。通过select @@autocommit;查询是否自动提交事务（1代表自动提交，0代表不自动提交）。执行set global autocommit=1;设置自动提交。

*注：这里设置自动提交事务操作针对高并发业务是会出问题的，所以视情况而定！*

