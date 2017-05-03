---
title: java定时任务Timer小试一下
date: 2016-09-14 15:16:26
categories: java
tags: java定时任务
---
java定时任务Timer 关于定时任务，似乎跟时间操作的联系并不是很大，但是前面既然提到了定时任务，索性在这里一起解决了。设置定时任务很简单，用Timer类就搞定了。

<!--more-->

### 一、我们定义一个需要定时执行的任务的类
```java
	package test; 
	import java.util.Timer; 
	public class TimeTaskTest {
		public static void main(String[] args){ 
			Timer timer = new Timer(); 
			timer.schedule(new Task(), 60 * 1000); 
		} 
	} 
```

注：我们的定时任务，就在这个类的main函数里执行

1、上面的代码实现了这样一个功能，当TimeTask程序启动以后，过一分钟后执行某项任务

2、很简单吧：先new一个Timer对象，然后调用它的schedule方法，这个方法有四个重载的方法，这里我们用其中一个，
public void schedule(TimerTask task,long delay) 

首先，第一个参数第一个参数就是我们要执行的任务。这是一个TimerTask对象，确切点说是一个实现TimerTask的类的对象，因为TimerTask是个抽象类。上面的代码里 面，Task就是我们自己定义的实现了TimerTask的类，因为是在同一个包里面，所以没有显性的import进来。Task类的代码如下：

```java
	package test; 

	import java.util.TimerTask; 

	public class Task extends TimerTask { 

		public void run(){ 
			System.out.println("定时任务执行"); 
		} 
	} 
```
我们的Task必须实现TimerTask的方法run，要执行的任务就在这个run方法里面，这里，我们只让它往控制台打一行字。

第二个参数第二个参数是一个long型的值。这是延迟的时间，就是从程序开始以后，再过多少时间来执行定时任务。这个long型的值是毫秒数，
所以前面我们的程序里面，过一分钟后执行用的参数值就是 60 * 1000。

### 二、循环执行任务

循环执行设置定时任务的时候，往往我们需要重复的执行这样任务，每隔一段时间执行一次，而上面的方法是只执行一次的，
这样就用到了schedule方法的是另一个重载函数
```java
	public void schedule(TimerTask task,long delay,long period) 	
```

前两个参数就不用说什么了，最后一个参数就是间隔的时间，又是个long型的毫秒数（看来java里涉及到时间的，跟这个long是脱不了干系了），
比如我们希望上面的任务从第一次执行后，每个一分钟执行一次，第三个参数值赋60 * 1000就ok了。

### 三、定时

指定执行时间既然号称是定时任务，我们肯定希望由我们来指定任务指定的时间，显然上面的方法就不中用了，因为我们不知道程序什么时间开始运行，
就没办法确定需要延时多少。没关系，schedule四个重载的方法还没用完呢。用下面这个就OK了：

```java
	public void schedule(TimerTask task,Date time) 
```
比如，我们希望定时任务2006年7月2日0时0分执行，只要给第二个参数传一个时间设置为2006年7月2日0时0分的Date对象就可以了。
有一种情况是，可能我们的程序启动的时候，已经是2006年7月3日了，这样的话，程序一启动，定时任务就开始执行了。

schedule最后一个重载的方法是
```java
	public void schedule(TimerTask task,Date firstTime,long period) 
```
### 四、扩展

j2ee中的定时任务在实际的项目中，往往定时任务需要对web工程中的资源进行操作，这样一来，用上面的单个程序的方式可能就有点力不从心了，因为很多web工程的资源它操作不到。解决的办法是，使用Servlet，把执行定时任务的那些代码放到Servlet的init()函数里就可以了，这个easy，就没有必要再写示例代码了吧

