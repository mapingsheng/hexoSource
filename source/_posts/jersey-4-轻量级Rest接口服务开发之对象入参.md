---
title: jersey-4--轻量级Rest接口服务开发之对象入参
date: 2017-08-11 10:35:29
categories: jersey
tags: java
---

本篇文章我主要描述一下通过javax.ws框架开发rest服务接口时，复杂一点的入参操作，主要包括对象入参、集合入参等等

<!--more-->

**一、新建一个控制器类**


> ParameterController

```java

	package com.maps.rest;

	import javax.ws.rs.Consumes;
	import javax.ws.rs.DefaultValue;
	import javax.ws.rs.GET;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.Produces;
	import javax.ws.rs.QueryParam;
	import javax.ws.rs.core.MediaType;
	
	import com.maps.vo.UserVO;
	
	@Path("param")
	@Consumes(MediaType.TEXT_PLAIN)
	public class ParameterController {
	
		
	}

```

上面的控制器将是我们下面要开发的rest接口服务类，rest接口访问入口为 */param*


**2. 新建一个封装入参的bean-UserVO**

```java

	package com.maps.vo;

	import java.io.Serializable;
	
	/**
	 * 用于封装入参数据的业务bean类，覆写了tostring方法
	 * @author mapingsheng
	 *
	 */
	public class UserVO implements Serializable{
	
		private static final long serialVersionUID = 6599357158756293288L;
		
		private String id;
		private String userName;
		private String passWord;
		private int age;
		private String email;
		private String address;
		
		
		public String getId() {
			return id;
		}
		public String getUserName() {
			return userName;
		}
		public String getPassWord() {
			return passWord;
		}
		public int getAge() {
			return age;
		}
		public String getEmail() {
			return email;
		}
		public String getAddress() {
			return address;
		}
		public void setId(String id) {
			this.id = id;
		}
		public void setUserName(String userName) {
			this.userName = userName;
		}
		public void setPassWord(String passWord) {
			this.passWord = passWord;
		}
		public void setAge(int age) {
			this.age = age;
		}
		public void setEmail(String email) {
			this.email = email;
		}
		public void setAddress(String address) {
			this.address = address;
		}
		@Override
		public String toString() {
			StringBuilder builder = new StringBuilder();
			builder.append("UserVO {id=").append(id).append(", userName=").append(userName).append(", passWord=")
					.append(passWord).append(", age=").append(age).append(", email=").append(email).append(", address=")
					.append(address).append("}");
			return builder.toString();
		}
	}

```


**2. 新建一个添加单个用户的rest接口方法-Json对象**

```java

	/**
	 * 该rest接口方法方法接受UserVO的json对象格式的参数数据
	 * @param user
	 * @return
	 */
	@POST
	@Path("addUser")
	@Consumes(MediaType.APPLICATION_JSON)
	public String paramTypForJson(UserVO user){

		return user.toString();

	}

```

*注：该rest接口方法为POST类型，接受的参数类型为json，响应为入参的对象的json字符串格式值*


该rest接口发布后，我们访问该rest接口

> 访问 http://localhost:8080/jersey/param/addUser

该rest接口的json对象请求数据将会到达对应方法体中，并且可以正常执行方法中的业务代码,并且把传递的json对象参数映射到UserVO对象上面并直接输出。

*注：因为我们该rest方法需要接收json类型的参数，所以需要在调用时header中限定Content-Type: application/json*

![Alt text](http://soujava.com/images/paramjsontype.png)

![Alt text](http://soujava.com/images/paramjsontype3.png)


**2. 新建一个添加批量用户的rest接口方法-List类型**

```java

	/**
	 * 该rest接口方法方法接受集合形式UserVO的json数组格式的参数数据
	 * @param user
	 * @return
	 */
	@POST
	@Path("addUserList")
	@Consumes(MediaType.APPLICATION_JSON)
	public String paramTypForJson(List<UserVO> userList){
		StringBuilder sf = new StringBuilder();
		for(UserVO user:userList){
			sf.append(user.toString()).append(" \n");
		}
		return sf.toString();
	}

```

*注：该rest接口方法为POST类型，接受的参数类型为json数组，响应为入参的集合中对象的json字符串格式值*

该rest接口发布后，我们访问该rest接口

> 访问 http://localhost:8080/jersey/param/addUserList

![Alt text](http://soujava.com/images/paramjsontype4.png)

![Alt text](http://soujava.com/images/paramjsontype5.png)



**2. 新建一个添加用户的rest接口方法-Map类型**

```java

	/**
	 * 该rest接口方法方法接受Map形式UserVO的json数组格式的参数数据
	 * @param user
	 * @return
	 */
	@POST
	@Path("addUserMap")
	@Consumes(MediaType.APPLICATION_JSON)
	public String paramTypForJson(Map<String,Object> map){
		StringBuilder sf = new StringBuilder();

		for(Map.Entry<String, Object> entry:map.entrySet()){
			String val = entry.getValue()+":"+entry.getValue();
			sf.append(val).append("  ");
		}

		return sf.toString();
	}

```

*注：上面的rest接口方法中，我们的入参是Map类型的，所以我们可以换一种思路理解，Map本来就是一定意义上的Json对象*

该rest接口发布后，我们访问该rest接口

> 访问 http://localhost:8080/jersey/param/addUserMap

![Alt text](http://soujava.com/images/paramjsontype6.png)

![Alt text](http://soujava.com/images/paramjsontype7.png)

