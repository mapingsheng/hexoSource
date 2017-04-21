---
title: java8使用stream对简单数据集排序
date: 2017-04-21 17:40:43
categories: java
tags: java
---
Stream是java8新增一个工具类，在java.util.stream.*;中，不得不说的是stream功能异常强大，一旦你接触之后就不想再用哪些旧的jdk中api方法了；下面先以日常开发中的集合数据排序为例慢慢了解stream中的sorted方法。

**一、对List数据集合排序**

>1、对List集合进行升序排列

```java
	
	public void streamSortTest1(){
  		//定义一个简单的List数据集合
  		List<Integer> list1 = Lists.newArrayList(1,3,2,5,4,6,8,2); 
		//直接调用sorted方法进行排序，val1-val2
  		list1 = list1.stream().sorted((val1,val2) ->val1-val2).collect(Collectors.toList());
  		System.out.println(list1);
	}

```

以上代码输出结果为：[1, 2, 2, 3, 4, 5, 6, 8]；可见排序结果按照 升序 规则进行排序处理

>2、对List集合进行降序排列

```java
	
	public void streamSortTest1(){
  		//定义一个简单的List数据集合
  		List<Integer> list1 = Lists.newArrayList(1,3,2,5,4,6,8,2); 
		//直接调用sorted方法进行排序，val2-val1
  		list1 = list1.stream().sorted((val1,val2) ->val2-val1).collect(Collectors.toList());
  		System.out.println(list1);
	}

```

以上代码输出结果为：[8, 6, 5, 4, 3, 2, 2, 1]；可见排序结果按照 降序 规则进行排序处理

>3、对List集合进行升序排列--自定义比较器

```java
	
	//先定义一个比较器，默认按照升序
	Comparator<Integer> compara = Comparator.comparing(u->u);
	//在sorted方法中使用自定义的比较器进行排序
	list1 = list1.stream().sorted(compara).collect(Collectors.toList());
	System.out.println(list1);

```

以上代码输出结果为：[1, 2, 2, 3, 4, 5, 6, 8]；可见排序结果按照 升序 规则进行排序处理


>4、对List集合进行降序排列--自定义比较器

```java
	
	//先定义一个比较器，默认按照升序
	Comparator<Integer> compara = Comparator.comparing(u->u);
	//在sorted方法中使用自定义的比较器进行排序，并且调用reversed方法反转了比较结果
	list1 = list1.stream().sorted(compara.reversed()).collect(Collectors.toList());
	System.out.println(list1);

```

以上代码输出结果为：[8, 6, 5, 4, 3, 2, 2, 1]；可见排序结果按照 降序 规则进行排序处理

>5、对Array数组进行升序排列

```java
	
	public void streamSortTest1(){
  		//定义一个简单的数组集合
  		Integer[] array1 = {1,3,2,5,4,6,8,2};
		//直接调用sorted方法进行排序,val1-val2
		array1 = Stream.of(array1).sorted((val1,val2) -> val1-val2).toArray(Integer[]::new);
		System.out.println(Arrays.asList(array1));
	}

```

以上代码输出结果为：[1, 2, 2, 3, 4, 5, 6, 8]；可见排序结果按照 升序 规则进行排序处理

>6、对Array数组进行降序排列

```java
	
	public void streamSortTest1(){
  		//定义一个简单的数组集合
  		Integer[] array1 = {1,3,2,5,4,6,8,2};
		//直接调用sorted方法进行排序，val2-val1
		array1 = Stream.of(array1).sorted((val1,val2) -> val2-val1).toArray(Integer[]::new);
		System.out.println(Arrays.asList(array1));
	}

```

以上代码输出结果为：[8, 6, 5, 4, 3, 2, 2, 1]；可见排序结果按照 降序 规则进行排序处理

>7、对Array数组进行升序排列--自定义比较器

```java
	
	public void streamSortTest1(){
  		//定义一个简单的数组集合
  		Integer[] array1 = {1,3,2,5,4,6,8,2};
		//自定义比较器，默认按照升序排序
		Comparator<Integer> compara = Comparator.comparing(u->u);
		//直接调用sorted方法进行排序
		array1 = Stream.of(array1).sorted(compara).toArray(Integer[]::new);
		System.out.println(Arrays.asList(array1));
	}

```

以上代码输出结果为：[1, 2, 2, 3, 4, 5, 6, 8]；可见排序结果按照 升序 规则进行排序处理

>8、对Array数组进行降序序排列--自定义比较器

```java
	
	public void streamSortTest1(){
  		//定义一个简单的数组集合
  		Integer[] array1 = {1,3,2,5,4,6,8,2};
		//自定义比较器，默认按照升序排序
		Comparator<Integer> compara = Comparator.comparing(u->u);
		//直接调用sorted方法进行排序，对比较器进行了反转处理，即按照降序排序
		array1 = Stream.of(array1).sorted(compara.reversed()).toArray(Integer[]::new);
		System.out.println(Arrays.asList(array1));
	}

```

以上代码输出结果为：[8, 6, 5, 4, 3, 2, 2, 1]；可见排序结果按照 降序 规则进行排序处理


是不是感觉用着非常愉快，其实对复杂的数据结构更能体现！