---
title: java新旧版本的排序
date: 2017-05-06 10:47:14
categories: java
tags: java
---
因为最近在做app客户的rest接口，所以要确保后台提供给app的rest接口是相对安全的，就涉及到了一些加密操作，其实主要涉及以下两种加密方式：

- AES加密
- MD5加密

其实针对上面两种加密方式我们应用在不同的场景，AES加密主要应用在POST、DELETE等对于服务器来讲不能确保”幂等“的操作；MD5加密主要应用在GET等对于服务器来讲可以确保“幂等”的操作。

一般是先确定待加密的value值集合，然后把value值集合进行自然排序，然后再加上双方约定好的秘钥串进行最终的MD5加密处理。

所以加密过程中为了确保双方的加密后的值能一致，都进行了排序处理，下面就针对排序这一操作在熟悉一下java中的排序。

<!--more-->

**一、自然排序**

自然排序就是不适用自定义的比较器进行排序，而是适用默认的比较器进行排序操作，比如经常使用的java.util包中的比较器工具类。

1. 对list集合自然排序--Collections.sort(list)

```java

	List<String> list = Arrays.asList("a","c","中","b");
	Collections.sort(list);
```

以上代码排序后结果：

>[a, b, c, 中]


2. 对Array数组自然排序--Arrays.sort(array)

```java

	String[] array = {"a","c","中","b"};
	Arrays.sort(array);

```	

以上代码排序后结果：

>[a, b, c, 中]


3. 对Map集合自然排序--Collections.sort(list)

```java

	Map<String,String> map = new HashMap<String,String>();
	map.put("name", "a");
	map.put("code", "c");
	map.put("real", "中");
	map.put("attr", "b");
	
	Collection<String> collect = map.values();
	List<String> mapList = new ArrayList<String>(collect);
	Collections.sort(mapList);

```

以上代码排序后结果：

>[a, b, c, 中]


注：可以看到以上所使用到的自然排序后，结果都是一样,按照升序进行排序。那么我们如果想让集合中的元素降序排列该怎么办呢，那就需要自定义比较器了。

**二、自定义比较器进行排序**

说到自定义比较器，就不得不说Comparator和Comparable这两个比较接口，下面进行各自说明一下

**1. Comparable接口**

该接口主要用来将自定义的类型具有排序特性，那么就需要实现该接口，该接口中只有一个compareTo方法，源码如下：

```java

public interface Comparable<T> {

	public int compareTo(T o);	
}

```

主要应用在以下场景

>针对集合中的泛型元素不具有排序特征(集合中的元素没有实现Comparable接口)，如果集合中的泛型元素不具有排序特性，那么我们就不能使用上面的例子中的默认比较器进行排序操作。

- 下面新建一个学生类-Student

```java

	public class Student{
	
		private String name; //名称
		private int age; //年龄
		
		Student(String name,int age){
			this.name = name;
			this.age = age;
		}
		
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public int getAge() {
			return age;
		}
		public void setAge(int age) {
			this.age = age;
		}
		
		@Override
		public String toString() {
			return "User [name=" + name + ", age=" + age + "]";
		}
	}

```

- 声明一个集合，并且把Student实例添加到集合中

```java

	Student stu1 = new Student("王明",25);
	Student stu2 = new Student("李茜",29);
	Student stu3 = new Student("魏泽",18);
	List<Student> stuList = Arrays.asList(stu1,stu2,stu3);

```

- 使用默认的集合排序方式对集合进行排序操作(无法编译)

```java

	Collections.sort(stuList); //编译报错

	//The method sort(List<T>) in the type Collections is not applicable for the arguments (List<Student>)

```
之所以我们针对泛型元素为Student类型的集合stuList进行默认排序时，无法通过编译，因为集合中的泛型元素Student没有不具有排序特性(Student没有实现Comparable接口)；

- 我们对Student类进行改造-实现Comparable接口，并覆写比较方法

```java
	/**
	 * 把Student类实现Comparable接口，然后该类就具有了排序特性，然后覆写比较方法
	 * @author maps
	 */
	public class Student implements Comparable<Student>{
	
		private String name;
		private int age;
		
		@Override
		public int compareTo(Student o) { //覆写比较方法，按照年龄升序排列
			return this.age-o.getAge();
		}
		
		Student(String name,int age){
			this.name = name;
			this.age = age;
		}
		
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public int getAge() {
			return age;
		}
		public void setAge(int age) {
			this.age = age;
		}
		
		@Override
		public String toString() {
			return "User [name=" + name + ", age=" + age + "]";
		}
	}

```

- 然后还使用默认的比较器进行排序操作

```java

	Student stu1 = new Student("王明",25);
	Student stu2 = new Student("李茜",29);
	Student stu3 = new Student("魏泽",18);
	List<Student> stuList = Arrays.asList(stu1,stu2,stu3);

	Collections.sort(stuList); //可以正常编译、运行

```

运行以上代码后输出：

>>[User [name=魏泽, age=18], User [name=王明, age=25], User [name=李茜, age=29]]

可以看到我们把集合中的泛型元素Student实现Comparable接口之后，该类就具有了排序特性，那么我们就可以使用sort方法进行排序了，不需要再针对包含该泛型元素的集合额外的自定义比较器了。

其实针对这个接口的作用主要就是将元素具有排序特性，那么该接口的使用有一定的局限性，也就是只能操作自己定义的类；比如上面例子中的Student类是导入第三方的，该类没有实现Comparable接口，我们也没有源码，那么我们现在要针对泛型元素为Student的集合元素进行排序时该怎么办呢？

然而我们针对那些不具有排序特性的泛型集合进行排序是很常见的操作，那么我们针对这种情况应该怎么办呢？那么我们可以通过使用Comparator接口来自定义比较器进行排序操作。

**2. Comparator接口**

该接口的功能就是自定义比较器操作，针对上面例子最后提出的问题我们接着分析。

该接口主要有以下两个应用场景：

>使那些不具备排序特性的元素可以进行排序操作

- 下面新建一个学生类-Student，该类仍然不具有排序特性

```java

	public class Student{
	
		private String name; //名称
		private int age; //年龄
		
		Student(String name,int age){
			this.name = name;
			this.age = age;
		}
		
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		public int getAge() {
			return age;
		}
		public void setAge(int age) {
			this.age = age;
		}
		
		@Override
		public String toString() {
			return "User [name=" + name + ", age=" + age + "]";
		}
	}

```

- 声明一个集合，并且把Student实例添加到集合中

```java

	Student stu1 = new Student("王明",25);
	Student stu2 = new Student("李茜",29);
	Student stu3 = new Student("魏泽",18);
	List<Student> stuList = Arrays.asList(stu1,stu2,stu3);

```

- 使用默认的集合排序方式对集合进行排序操作(无法编译)

```java

	Collections.sort(stuList); //编译报错

```

- 我们定义一个针对不具有排序特性的Student的元素集合定义一个比较器

```java

	/**
	 * 定义一个普通的类，然后把该类实现Comparator排序接口即可，泛型类型就是我们待排序的Student类
	 * @author maps
	 */
	public class StudentComparator implements Comparator<Student>{

		@Override
		public int compare(Student o1, Student o2) {
			return o1.getAge()-o2.getAge();
		}
	}

```

- 使用我们自定义的比较器对刚才的stuList集合进行排序操作

```java

	Collections.sort(stuList, new StudentComparator());

```
上面代码执行结果：

>> [User [name=魏泽, age=18], User [name=王明, age=25], User [name=李茜, age=29]]

可以看到使用我们自定义的比较器后，排序时使用了集合中的泛型元素的年龄(age)属性进行了升序排列。


>自定义一个比较器，然后覆盖默认的排序方法

其实这种使用场景在上面已经用到了，就是下面一行代码

>>Collections.sort(stuList, new StudentComparator());

我们可以看到Collections.sort方法定义了两个不同参数格式的重载方法，其中一个参数的sort方法就是使用默认的比较器(集合中泛型元素实现接口Comparable后覆写的比较方法)进行排序的操作；其中两个参数的sort方法中第二个参数就是我们自定义的比较器实例，所以当我们传递第二个参数时，也就表明我们将使用我们自定义的比较器来覆盖默认的比较器进行排序操作，具体就不进行演示了，上面例子中已经展示了。

其实Comparator接口中定义了很多有用的方法，比如把排序规则反转、构件排序链等等，比如我们定义的比较器是按照学生的年龄进行升序排序的，那么我们可以使用比较器的反转方法进行降序排列。

```java

	Collections.sort(stuList,new StudentComparator().reversed());

```

上面代码运行结果：

>[User [name=李茜, age=29], User [name=王明, age=25], User [name=魏泽, age=18]]

可以看到我们使用比较器反转方法后，按照了年龄降序进行排序。