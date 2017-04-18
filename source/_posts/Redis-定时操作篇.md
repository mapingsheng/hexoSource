---
title: Redis-定时操作篇
date: 2017-04-18 17:09:35
categories: Redis
tags: Redis
---
**一、定时描述**
平时开发中，经常会把一些热数据存入缓存中，并且设置失效时间，比如把用户和rest接口地址存入缓存，并设置失效时间，一方面可以提高响应速度，还可以把一部分无效的请求挡在缓存层处理；还比如把验证码存入缓存，并设置失效时间，也可以达到上述目的。redis缓存有两种定时模式：

>1、设置某键值在多少秒之后失效
>
>2、设置某键值在以后某一个时间点之后失效，比如2017年10月1日23点59分59秒失效

**二、设置某键值在多少秒之后失效**

>**EXPIRE key seconds**
>
>为给定 key 设置生存时间，当 key 过期时(生存时间为 0 )，它会被自动删除;当 key 不存在或者不能为 key 设置生存时间时(比如在低于 2.1.3 版本的 Redis 中你尝试更新 key 的生存时间)，返回 0。
>
>可以对一个已经带有生存时间的 key 执行 EXPIRE 命令，新指定的生存时间会取代旧的生存时间。
>
>注：Redis 2.4 版本中，就算 key 已经过期，但还是可能在过期之后一秒钟之内被访问到；而在新的 Redis 2.6 版本中，延迟已经精确到1毫秒。

```java

	127.0.0.1:6379> set username maps #先设置键-值

	OK

	127.0.0.1:6379> ttl username  #此时还没有为key设置失效时间，所以用ttl时返回-1
	
	(integer) -1


	127.0.0.1:6379> expire username 30 #设置过期时间为30秒

	(integer) 1

	127.0.0.1:6379> ttl username #查看剩余失效时间

	(integer) 25

	127.0.0.1:6379> ttl username  #查看剩余失效时间

	(integer) 23

	127.0.0.1:6379> ttl username  #查看剩余失效时间

	(integer) 22

	127.0.0.1:6379> expire username 50  #重新设置(更新）失效时间为50秒，如果key已经失效，则返回0

	(integer) 1

	127.0.0.1:6379> ttl username #查看剩余失效时间

	(integer) 47

	127.0.0.1:6379> ttl username #查看剩余失效时间

	(integer) 46

	127.0.0.1:6379> ttl username #当该键已经达到失效时间时，则自动失效，ttl查看时返回-2

	(integer) -2

	127.0.0.1:6379> keys username  #可以通过keys命令查看已经失效的键是否存在

	(empty list or set)

```

**三、设置某键值在以后某一个时间点之后失效**

>**EXPIREAT key timestamp**
>
>将key键在将来UNIX时间戳timestamp点失效
>
>如果生存时间设置成功，返回 1，当 key 不存在或没办法设置生存时间，返回 0
>
>注：EXPIREAT的设置规则跟EXPIRE命令一样

```java

	127.0.0.1:6379> set username maps #先设置键-值

	OK

	127.0.0.1:6379> ttl username  #此时还没有为key设置失效时间，所以用ttl时返回-1
	
	(integer) -1
	
	127.0.0.1:6379> expireat username 1491494400000 # 这个 key 将在 2017-04-07 00:00:00 过期

	(integer) 1

	127.0.0.1:6379> ttl username  #查看剩余失效时间 

	(integer) 1490001890019
	
```