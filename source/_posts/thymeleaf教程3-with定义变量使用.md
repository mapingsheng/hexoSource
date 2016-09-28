---
title: thymeleaf教程3--with定义变量使用
date: 2016-09-28 16:11:44
categories: thymeleaf
tags: thymeleaf
---
在使用thymeleaf渲染数据的时候，我们经常会用到basepath变量（访问地址前缀），一般我们在jsp中直接request对象获取拼接而成，但是在thymeleaf中如何使用呢，可以通过th:with定义并使用。

### th:with用法

1、用来定义局部变量，用法如下：
```java
	<div th:with="name=zhangsan">  
    	<p>hello <span th:text="${name}"></span>.</p>  
	</div> 
```

上面的例子定义了一个变量name，值为zhangsan，然后我们在p标签中输出定义的变量。

通过上面的例子可以看出来用法规则为--th:with="key=value"这种格式定义局部变量

2、用来定义多个局部变量，用法如下：

```java
	<div th:with="name=zhangsan,age=25">  
    	<p>hello <span th:text="${name}"></span>.</p>  
	</div> 
```

上面的例子定义了第一个变量name，值为zhangsan;第二个变量age，值为25。


### th:with范围

1、我们在定义变量的时候其实是存在范围限制的，我们定义的变量，只能在我们所定义的标签内部使用
```java
	<div th:with="name=zhangsan">  
    	<p id="inner">hello <span th:text="${name}"></span>.</p>  
	</div> 
	<p id="outer">hello <span th:text="${name}"></span>.</p>  
```

上面的例子中我们在div标签中定义了一个name变量，然后分别在div标签内部和外部的p标签上面进行使用，其实在id为outer的p标签上面是不能取到变量

name的值的。因为我们定义的变量name是在div标签上面的，所以只能在div标签内部进行使用。这就是范围

### th:with定义对象

我们前面仅仅使用th:with定义简单的变量，其实也可以用来定义对象，比如我们通过后台返回的数据封装VO对象，我们就可以在这里定义，

```java
	<div th:with="user=${userList[0]}">  
    	<p>The name of the first person is <span th:text="${user.userName}">Julius Caesar</span>.</p>  
	</div>  
```

我们定义变量user为list中的一个索引对象，然后我们就可以使用变量中的属性了。


### th:with定义的变量在js中如何使用

```java
	<head th:with="name=zhangsan"> 
		<script type="text/javascript" th:inline="javascript">
			var name = [[${columnListJson}]];
		</script>

	</head>
	
```
上面的例子中有三个地方需要注意：

1、如果在js中需要使用变量，那么就要考虑前面提到的使用范围问题

2、如果在js中使用那么需要在<script>标签中添加 th:inline="javascript"

3、在js标签中获取变量时，需要采用[[$变量名称]]的方式获取使用






