---
title: Mysql数据库编码更改
date: 2016-09-20 10:46:29
categories: Mysql
tags: Mysql
---
在平时的开发过程中，mysql的编码是拉丁字符，所以当向里面存储数据时，显示出来就是乱码，那么我们需要执行如下操作：

### 1.通过命令或者其他终端登录到mysql服务中，并执行查询目前对应的数据库的编码信息sql脚本查询，执行命令会查询出该数据库下面每一张表对应的字符编码信息

<!--more-->

![Alt text](http://soujava.com/images/mysqlCode1.png)

### 2.然后执行下面的sql脚本，基于查询语句生成修改表结构编码的sql语句列表
```java
	select concat('alter table ', table_name,' character set utf8 collate utf8_bin;') from information_schema.tables as t, information_schema.COLLATION_CHARACTER_SET_APPLICABILITY as c 
	where c.collation_name = t.table_collation and t.table_schema='confluence' and (c.character_set_name!='utf8' or c.collation_name!='utf8_bin');	
```

### 3.然后把第2步查询出的alter语句复制到对应的命令窗口中执行修改具体的表结构编码操作

![Alt text](http://soujava.com/images/mysqlCode2.png)