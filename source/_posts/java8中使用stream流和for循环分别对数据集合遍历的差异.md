---
title: java8中使用stream流和for循环分别对数据集合遍历的差异
date: 2016-08-31 09:44:41
categories: java
tags: java8 Stream
---
最近在平时开发中越来越多的会用到java8新增的流处理功能Stream；用一次就上瘾的感觉啊！在处理一个稍微大一些的数据集合时（长度为10W的List<Map<String,Object>>）,刚好对比一下用java8的stream遍历集合和用java的for循环遍历集合的效率差异！

### 一、分别比较用两种方式遍历集合并输出集合中元素的效率(不在循环中加工组装集合数据) ###

**1、声明个长度为10W的集合**
```java
		List<Map<String,Object>> listMap = new ArrayList<Map<String,Object>>();
	    for(int i=0;i<100000;i++){
	    	Map<String,Object> map = new HashMap<String,Object>();
		    map.put("id", i);
		    map.put("title", "title"+i);
		    map.put("view", "view"+i);
		    listMap.add(map);
	    }
```

**2、使用java8的stream的forEach遍历集合并输出**
```java
	listMap.stream().forEach(p -> System.out.println(p.get("id")+" "+p.get("title")));
```
控制台打印内容如下：花费1582毫秒
```java
	..... ..........
	99996 title99996
	99997 title99997
	99998 title99998
	99999 title99999
	花费时间：1582
```
**3、使用for循环遍历集合并输出**
```java
	 for(Map map:listMap){
	    System.out.println(map.get("id")+" "+map.get("title"));
	 }
```
控制台打印内容如下：花费1548毫秒
```java
	99995 title99995
	99996 title99996
	99997 title99997
	99998 title99998
	99999 title99999
	花费时间：1548
```
**4、小结**
 
 长度为1W时：  for循环【170毫秒】    stram循环 【240毫秒】

 长度为10W时： for循环【1548毫秒】    stram循环【1582毫秒】

 长度为100W时：for循环【21099毫秒】   stram循环【20291毫秒】

 所以个人经过上面的测试发现其实两种方式去遍历集合的效率是差不多的！

### 二、分别比较用两种方式遍历集合并输出集合中元素的效率(在循环中加工组装集合数据) ###

**1、声明个长度为10W的集合**
```java
		List<Map<String,Object>> listMap = new ArrayList<Map<String,Object>>();
	    for(int i=0;i<100000;i++){
	    	Map<String,Object> map = new HashMap<String,Object>();
		    map.put("id", i);
		    map.put("title", "title"+i);
		    map.put("view", "view"+i);
		    listMap.add(map);
	    }
```
**2、声明个长度为2的集合**
```java
		//该集合中的key键就是上面长度为10W集合中的id的值
		Map<String,Object> mapTop = new HashMap<String,Object>();
	    mapTop.put("10", "1");
	    mapTop.put("90", "0");
```
接下来我们分别用for循环和stream流循环遍历集合并且在遍历过程中向集合中追加一个新的键值对数据top:XX;

**3、用java8的stream流遍历**
```java
	listMap.stream().map((s)->{
				return s.put("top",mapTop.get(String.valueOf(s.get("id"))));
			}).collect(Collectors.toList());
```
上面的代码中我们通过stream流中的map进行数据追加：
追加前的集合数据结构：[{view=view0,id=0, title=title0},{view=view0,id=10, title=title0},...]
追加后的集合数据结构：[{view=view0, top=null, id=0, title=title0},{view=view10, top=1, id=10, title=title10},...]

花费时间：4641毫秒

**4、用for循环遍历**
```java
	 for(Map map:listMap){
	    	map.put("top", mapTop.get(String.valueOf(map.get("id"))));
	 }
```

花费时间：5513毫秒

**5、小结**

当分别使用两种方式对同一集合进行遍历并且数据追加时，效率也是不相上下的，使用stream流循环稍微快那么一丁点！

后来通过“科学上网”的方式，搜集到了一篇老外写的关于stream循环和for循环的文章，说的是意思是不应该用stream循环替换你的for循环！个人感觉这也因场景而定，不能一概而论，首先比如在循环过程中如果涉及复杂的数据装载和加工，那么个人感觉使用for循环比较好理解；反之则使用stream流的方式比较好；并且我们不能仅仅因为循环这一动作就下结论不太科学，比如stream的分组、过滤等等其他功能是for循环所不能及的！

![Alt text](http://soujava.com/images/javaStream2.png)
