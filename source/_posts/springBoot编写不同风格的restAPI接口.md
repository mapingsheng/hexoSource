---
title: springBoot编写不同风格的restAPI接口
date: 2016-08-31 19:38:46
categories: spring
tags: springBoot
---
平时使用springboot开发后台功能模块，并且开发对外提供的restfulApi接口时也同样使用springBoot开发，目前主要用到两种方式的的restfulApi;下面分两点说明

### 一、静态url+动态参数(param)形式的api ###
接口形式：

```java
	http://localhost:8080/api/user/info?id=123
```

api代码：

```java
	@RestController
	@RequestMapping("/api")
	public class userApiController {
		
		@Autowired
		private UserService userService;

		/**
		  * 根据用户id获取用户信息的api
		  * @param id 用户id
		  */
		@RequestMapping(value = "user/info")
		public void user(@RequestParam(value = "id") String id）{
				方法体
		}
	}
```

上面的例子中使用了如下个注解：
1、@RestController  用来标记这是一个支持REST的控制器，其实其是@Controller扩展来的
2、@RequestMapping  其提供路由信息，它告诉Spring任何来自"/"路径的HTTP请求都应该被映射到该Controller入口中
3、@Autowired  对类成员变量、方法及构造函数进行注解，完成自动装配的工作。 用来消除set ，get方法
3、@RequestParam 将请求参数区数据映射到功能处理方法的参数上，比如我们的@RequestParam(value="id") String userId这样，我们对外公开的参数名称为id，而我们业务方法体用的参数名称为userId，该注解就是将两个参数绑定在一起


### 二、动态url+动态参数(param)形式的api ###

接口形式：

```java
	http://localhost:8080/api/user/{id}/info?status=1
```

api代码：

```java
	@RestController
	@RequestMapping("user/{id}/info")
	public class userApiController {
		
		@Autowired
		private UserService userService;

		/**
		  * 根据用户id获取用户信息的api
		  * @param id 用户id
		  */
		@RequestMapping(value = "user/info")
		public void user(@PathVariable(value = "id") String id,
						 @RequestParam(value = "status") String status）{
				方法体
		}
	}
```

上面的例子中使用了如下个注解：
1、@PathVariable 将方法中的参数绑定到请求url中(path)中的动态变量，比如上面例子中将path中的{id}绑定到user方法中的String id参数上面


### 三、总结 ###

1、如果参数不在path路径中动态传递(所有参数在?后面以键值对传递)，那么方法体中使用@RequestParam进行参数绑定(如果传递的参数名称跟方法中传递的参数名称一致的话可以不用改注解)

2、如果参数在path路径中动态传递(在?前面的path路径中使用{id}这种方式传递参数，那么方法体中必须使用@PathVariable进行参数绑定)