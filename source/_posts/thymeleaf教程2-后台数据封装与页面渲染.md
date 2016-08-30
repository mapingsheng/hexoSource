---
title: thymeleaf教程2-后台数据封装与页面渲染
date: 2016-08-23 16:32:50
categories: thymeleaf
tags: thymeleaf
---
我们使用模板引擎的过程中，页面上面的数据肯定需要从后台传递过来，然后把数据装载到页面对应区域中，所以我们来看看如果把后台的数据在前端页面渲染。

**一、添加一个表单页面--登陆操作**
![](http://soujava.com/images/thy6.png)

**二、新建业务控制器(doLogin)--获取登陆表单中提交的数据**
```java
		String username = request.getParameter("username");
    	String password = request.getParameter("password");
    	//实例化一个thymeleaf文本上下文
        WebContext ctx = new WebContext(request, response, servletContext, request.getLocale());
		//将前端登陆表单页面提交过来的数据封装到thymeleaf上下文中
        ctx.setVariable("username", username);
        ctx.setVariable("password", password);
        //调用模板引擎转向到对应的模板页面--desc模板页面
        templateEngine.process("desc", ctx, response.getWriter());
```
**三、新建模板页面(desc)--显示doLogin控制器中封装的数据**

![](http://soujava.com/images/thy7.png)

*注：读取数据使用${XX}这种形式，是不是感觉很熟悉，EL表达式；而向页面html元素中渲染数据使用thymeleaf提供的标签th:text="${XX}"这种形式。*


**四、编译工程项目--启动web服务器测试**

1、在登陆表单中输入对应的用户名和密码，点击提交按钮
![](http://soujava.com/images/thy1.png)

1、可以看到提交表单后进入到doLogin控制器中，控制器获取数据并封装数据，然后转向到模板页面中渲染数据。
![](http://soujava.com/images/thy2.png)


**五、在控制器doLogin中封装一些稍微复杂的数据集-数组**

1、我们声明一个类型数组，然后把数组封装到thymeleaf上下文中
```java
	String[] types = {"足球","篮球","羽毛球"};
    ctx.setVariable("types", types);
```

2、在desc模板页面中遍历显示封装好的集合数据

```java
	<p th:each="type : ${types}">
    	<span th:text="${type}"></span>
    </p>
```
*注：上面渲染集合数据到模板页面的代码中我们采用了th:each的方法遍历数据，这是thymeleaf提供的，其实thymeleaf提供了非常多并且非常强大的方法，后续再一一说明。*

3、针对刚刚封装好的集合数据到页面测试一下

![](http://soujava.com/images/thy3.png)

![](http://soujava.com/images/thy4.png)


**六、在控制器doLogin中封装一些稍微复杂的数据集-实体Bean**

1、我们新建一个User的Bean实体
```java
	public class User {
	 	private String firstName = null;
	    private String lastName = null;
	    private String nationality = null;
	    private Integer age = null；
	    
	    public User(final String firstName, final String lastName, 
	            final String nationality, final Integer age) {
	        super();
	        this.firstName = firstName;
	        this.lastName = lastName;
	        this.nationality = nationality;
	        this.age = age;
	    }
	}
```

2、在我们的doLogin业务控制器中实例化该User实体bean，并且把bean数据实体封装到thymeleaf上下文中

```java
		User user = new User("zhang","san","china",25);
        ctx.setVariable("user", user);
```

3、在desc模板页面中显示封装好的User实体bean数据
```java

	<p th:text="${user.firstName+':'+user.lastName+':'+user.nationality+':'+user.age}"></p>
```
注：我们在获取bean中的属性数据时，仍然采用EL表达式${user.firstName}的方式；然后将获取的数据显示到html元素中用th:text方法；只不过我们使用th:text时在中间使用了字符串拼接将多个属性拼接显示。

**六、总结**

1、我们在执行业务操作的时候，需要把后台的数据集封装到thymeleaf上下文中，我们可以封装组合各种我们需要的数据，如：字符串、集合、实体bean等等
2、通过thymeleaf模板引擎转向到对应的模板页面
3、用thymeleaf提供的方法渲染后台封装的数据到页面中