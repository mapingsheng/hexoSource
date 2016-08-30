---
title: MySql安装后添加的用户不能远程连接登陆
date: 2016-08-19 16:23:54
categories: Mysql
tags: Mysql
---

**一、添加一个用户fileserver并授权操作**
![Alt text](http://soujava.com/images/mysql1.png)

*注：添加一个可以访问所有数据库、用户名为fileserver、可以在本地和远程进行访问的、密码是fileserver的用户 *

**二、查询刚才添加的用户是否存在**
![Alt text](http://soujava.com/images/mysql2.png)

*注：可以看到刚才的fileserver用户已经创建完毕 *

**三、用刚才添加成功的用户登陆操作报异常**
![Alt text](http://soujava.com/images/mysql3.png)

*注：当用户刚才创建的用户登录并输入密码后时提示如下信息，这是因为当前的登录操作是在mysql本地客户端进行的，而上面的user表中没有对应localhost的fileserve的用户*

**四、在添加一个fileserver用户并分配可以本地localhost登录**
![Alt text](http://soujava.com/images/mysql4.png)

**五、再查下刚才创建的用户是否已经存在**
![Alt text](http://soujava.com/images/mysql5.png)


**六、再次用刚刚创建的fileserver登录操作，可以正常登录**
![Alt text](http://soujava.com/images/mysql6.png)
*注：使用mysql -u 命令登录操作时默认使用localhost的域用户登录操作 *

**七、在另外一台IP地址的电脑上面安装Navicate工具，并且连接之前安装的mysql服务**
![Alt text](http://soujava.com/images/mysql7.png)
*注：连接时提示连接错误，因为mysql服务没有对当前IP的电脑开放远程访问的权限*

**八、在mysql配置文件中配置该IP的访问操作**
![Alt text](http://soujava.com/images/mysql8.png)

**九、配置完成后重启mysql服务**
![Alt text](http://soujava.com/images/mysql9.png)

**十、再次在不同IP(192.168.0.203)地址电脑上面通过Navicate连接mysql服务操作，连接成功**
![Alt text](http://soujava.com/images/mysql10.png)