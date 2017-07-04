---
title: jersey-2--轻量级Rest接口后台服务常规接口开发
date: 2017-07-04 15:27:35
categories: jersey
tags: java
---

随着上一篇文章讲解了jersey框架的搭建运行，这一篇主要围绕我们平时开发rest接口方式进行示例；平时开发rest接口时无非提供GET、POST、PUT、DELETE等等类型的方法，或者在header中传入一些验证参数，下面我们就以user为例，主要围绕user的增删改查行为进行开发对应的rest接口。

<!--more-->

**一、新建rest接口数据封装VO类-UserVO**

```java

	package com.maps.vo;

	import java.io.Serializable;
	
	import javax.xml.bind.annotation.XmlAccessType;
	import javax.xml.bind.annotation.XmlAccessorType;
	import javax.xml.bind.annotation.XmlElement;
	import javax.xml.bind.annotation.XmlRootElement;
	
	@XmlRootElement
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
			builder.append("UserVO [id=").append(id).append(", userName=").append(userName).append(", passWord=")
					.append(passWord).append(", age=").append(age).append(", email=").append(email).append(", address=")
					.append(address).append("]");
			return builder.toString();
		}
	}


```

**二、新建rest接口服务类-UserController**


**1. 【GET】创建返回json类型的rest接口方法**

```java

	package com.maps.rest;

	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.Map;
	
	import javax.ws.rs.Consumes;
	import javax.ws.rs.DELETE;
	import javax.ws.rs.GET;
	import javax.ws.rs.HeaderParam;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.PathParam;
	import javax.ws.rs.Produces;
	import javax.ws.rs.core.MediaType;
	import com.maps.vo.UserVO;
	
	@Path("user")
	public class UserController {
			
		/**
		 * 获取单个用户数据的rest接口，并且返回数据格式为json类型
		 * @return
		 */
		@GET
		@Path("/info/json")
		@Produces("application/json")
	    public UserVO info() {
			
			UserVO user = new UserVO();
			user.setId("1");
			user.setUserName("zhansan");
			user.setAge(25);
			user.setPassWord("123456");
			user.setEmail("asfdsa@199ta.com");
			user.setAddress("大山中");
	        return user;
	    }
		
		/**
		 * 获取用户列表数据的rest接口，并且返回数据格式为json类型
		 * @return
		 */
		@GET
		@Path("/info/json/list")
		@Produces(MediaType.APPLICATION_JSON)
		public List<UserVO> infoList(){
			List<UserVO> userList = new ArrayList<UserVO>();
			UserVO user = new UserVO();
			user.setId("1");
			user.setUserName("zhansan");
			user.setAge(25);
			user.setPassWord("123456");
			user.setEmail("asfdsa@199ta.com");
			user.setAddress("大山中");
			
			UserVO user2 = new UserVO();
			user2.setId("1");
			user2.setUserName("李四");
			user2.setAge(25);
			user2.setPassWord("111111");
			user2.setEmail("wedaf@199ta.com");
			user2.setAddress("宋城路");
			
			userList.add(user);
			userList.add(user2);
			
			return userList;
		}
		
	}


```

*启动tomcat，访问rest接口*

> http://localhost:8080/jersey/user/info/json

返回json对象类型的数据

```java

	{
		userName: "zhansan",
		passWord: "123456",
		age: 25,
		email: "asfdsa@199ta.com",
		address: "大山中",
		userId: "1"
	}

```

> http://localhost:8080/jersey/user/info/json/list

返回json数组类型的数据

```java

	[
		{
			userName: "zhansan",
			passWord: "123456",
			age: 25,
			email: "asfdsa@199ta.com",
			address: "大山中",
			userId: "1"
		},
		{
			userName: "李四",
			passWord: "111111",
			age: 25,
			email: "wedaf@199ta.com",
			address: "宋城路",
			userId: "1"
		}
		]

```

**2. 【GET】创建返回xml类型的rest接口方法**

```java

	package com.maps.rest;

	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.Map;
	
	import javax.ws.rs.Consumes;
	import javax.ws.rs.DELETE;
	import javax.ws.rs.GET;
	import javax.ws.rs.HeaderParam;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.PathParam;
	import javax.ws.rs.Produces;
	import javax.ws.rs.core.MediaType;
	import com.maps.vo.UserVO;
	
	@Path("user")
	public class UserController {
		
		/**
		 * 获取单个用户对象数据的rest接口，并且返回数据格式为xml类型
		 * @return
		 */
		@GET
		@Path("/info/xml")
		@Produces("application/xml")
		public UserVO infoXml(){
			UserVO user = new UserVO();
			user.setId("1");
			user.setUserName("zhansan");
			user.setAge(25);
			user.setPassWord("123456");
			user.setEmail("asfdsa@199ta.com");
			user.setAddress("大山中");
			
			return user;
		}
		
		/**
		 * 获取用户列表rest接口，并且返回数据格式为xml类型
		 * @return
		 */
		@GET
		@Path("/info/xml/list")
		@Produces("application/xml")
		public List<UserVO> infoXmlList(){
			List<UserVO> userList = new ArrayList<UserVO>();
			UserVO user = new UserVO();
			user.setId("1");
			user.setUserName("zhansan");
			user.setAge(25);
			user.setPassWord("123456");
			user.setEmail("asfdsa@199ta.com");
			user.setAddress("大山中");
			
			UserVO user2 = new UserVO();
			user2.setId("1");
			user2.setUserName("李四");
			user2.setAge(25);
			user2.setPassWord("111111");
			user2.setEmail("wedaf@199ta.com");
			user2.setAddress("宋城路");
			
			userList.add(user);
			userList.add(user2);
			
			return userList;
		}
	}

```

> http://localhost:8080/jersey/user/info/xml

返回xml对象类型的数据

```java
	
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<userVO>
		<userId>1</userId>
		<userName>zhansan</userName>
		<passWord>123456</passWord>
		<age>25</age>
		<email>asfdsa@199ta.com</email>
		<address>大山中</address>
	</userVO>

```

> http://localhost:8080/jersey/user/info/xml/list

返回xml集合类型的数据

```java
	
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<userVOes>
		<userVO>
			<userId>1</userId>
			<userName>zhansan</userName>
			<passWord>123456</passWord>
			<age>25</age>
			<email>asfdsa@199ta.com</email>
			<address>大山中</address>
		</userVO>
		<userVO>
			<userId>1</userId>
			<userName>李四</userName>
			<passWord>111111</passWord>
			<age>25</age>
			<email>wedaf@199ta.com</email>
			<address>宋城路</address>
		</userVO>
	</userVOes>

```

**3. 【GET】动态url请求rest接口-动态传参**

在请求url中动态传参

```java

	package com.maps.rest;

	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.Map;
	
	import javax.ws.rs.Consumes;
	import javax.ws.rs.DELETE;
	import javax.ws.rs.GET;
	import javax.ws.rs.HeaderParam;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.PathParam;
	import javax.ws.rs.Produces;
	import javax.ws.rs.core.MediaType;
	import com.maps.vo.UserVO;
	
	@Path("user")
	public class UserController {
		

		/**
		 * 在请求url路径中动态传递参数
		 * @param id 用户id
		 * @return
		 */	
		@GET
		@Path("/path/{id}")
		@Produces({"application/json"})
		public UserVO pathJson(@PathParam("id") String id){
			
			UserVO user = new UserVO();
			user.setId(id);
			user.setUserName("zhansan");
			user.setAge(25);
			user.setPassWord("123456");
			user.setEmail("asfdsa@199ta.com");
			user.setAddress("大中华");
			
			return user;
		}	
	}


```

> http://localhost:8080/jersey/user/path/path中动态传参

```java

	{
		userName: "zhansan",
		passWord: "123456",
		age: 25,
		email: "asfdsa@199ta.com",
		address: "大中华",
		userId: "path中动态传参"
	}

```

**4. 【POST】创建post类型，并且只接受json格式数据体的rest方法**

```java

	package com.maps.rest;

	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.Map;
	
	import javax.ws.rs.Consumes;
	import javax.ws.rs.DELETE;
	import javax.ws.rs.GET;
	import javax.ws.rs.HeaderParam;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.PathParam;
	import javax.ws.rs.Produces;
	import javax.ws.rs.core.MediaType;
	import com.maps.vo.UserVO;
	
	@Path("user")
	public class UserController {
		
		private static Map<String,UserVO> mapTemp = new HashMap<String, UserVO>();
		

		/**
		 * 添加用户的rest接口，
		 * @param user json数据格式的user对象参数
		 * @return
		 */
		@POST
		@Path("/addUser")
		@Consumes({"application/json"})
		@Produces({"application/json"})
		public List<UserVO> addUser(UserVO user){
			userListTemp.add(user);
			
			return userListTemp;
		}
		
	}

```

> 通过rest接口工具调用

![Alt text](http://soujava.com/images/restinvokepost.png)

![Alt text](http://soujava.com/images/restinvokepostresponse.png)


**5. 【POST】创建post类型，并且只接受json格式数据体的rest方法,同时需要在header中传递校验码参数**

```java

	package com.maps.rest;

	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.Map;
	
	import javax.ws.rs.Consumes;
	import javax.ws.rs.DELETE;
	import javax.ws.rs.GET;
	import javax.ws.rs.HeaderParam;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.PathParam;
	import javax.ws.rs.Produces;
	import javax.ws.rs.core.MediaType;
	import com.maps.vo.UserVO;
	
	@Path("user")
	public class UserController {
		
		private static Map<String,UserVO> mapTemp = new HashMap<String, UserVO>();
		
		/**
		 * 添加用户的rest接口，并且header中需要传递对应的md5校验值
		 * @param user json数据格式的use对象参数
		 * @param md5val  在请求的header中传递的校验码
		 * @return
		 */
		@POST
		@Path("/addUser/headCheck")
		@Consumes({"application/json"})
		@Produces({"application/json"})
		public Map<String,UserVO> addUser(UserVO user,@HeaderParam("md5val") String md5val){
			mapTemp.put(md5val, user);
			
			return mapTemp;
		}
		
	}


```

> 通过rest接口工具调用

![Alt text](http://soujava.com/images/restpostheader.png)

![Alt text](http://soujava.com/images/restpostheaderresult.png)


**5. 【DELETE】创建delete类型的rest方法，删除用户操作**

```java

	package com.maps.rest;
	
	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.Map;
	
	import javax.ws.rs.Consumes;
	import javax.ws.rs.DELETE;
	import javax.ws.rs.GET;
	import javax.ws.rs.HeaderParam;
	import javax.ws.rs.POST;
	import javax.ws.rs.Path;
	import javax.ws.rs.PathParam;
	import javax.ws.rs.Produces;
	import javax.ws.rs.core.MediaType;
	import com.maps.vo.UserVO;
	
	@Path("user")
	public class UserController {
		
	
		/**
		 * 通过动态传参的方式，创建一个delete方法类型删除用户的rest接口，返回一个字符串状态
		 * @param id
		 * @return
		 */
		@DELETE
		@Path("/delete/{id}")
		@Produces(MediaType.TEXT_PLAIN)
		public String deleteUser(@PathParam("id") String id){
			System.out.println("DELETE  -- deleteUser by "+id);
			return "success";
		}
		
	}

```

> 通过rest接口工具调用

![Alt text](http://soujava.com/images/deleteinvoke.png)

![Alt text](http://soujava.com/images/deleteresult.png)


截止目前我们已经把我们常规的rest接口的开发方式都实现了，在实现过程中也看到了我们都是用注解去处理的，那么下面我们大概讲述一下每一个rest接口对应的不同的注解的作用。

**二、注解描述**

> **请求方式 @GET @POST @DELETE**

> - 在上面的REST接口对应的方法中都有对应的方法类型(rest接口的请求方式)，添加对应的注解，该rest接口就需要用什么类型的方式进行请求


> **接口地址 @Path**

> - 该注解就如同springmvc中的@RequestMapping注解一样的作用，主要用来指定该方法对应的rest接口的访问地址是什么

> **动态传参 @PathParam("id")**

> - 这个注解一般配合注解@Path一起使用，当在@Path注解中指定对应的动态参数，然后在方法参数中使用注解@PathParam进行绑定，该参数相当于springmvc中的@PathVariable("id")注解的作用

> **请求参数类型(接受的参数类型) @Consumes("application/json")**

> - 我们不管使用get请求rest接口，或者使用post请求rest接口，一般都需要传参，该注解就是用来限制对应的rest方法中所要接受的对应参数类型的，上面的例子我们指定对应的rest接口只接受对应的json类型的参数；当然该注解其实可以使用对应的枚举赋值，后续开发中最好使用枚举进行复制-@Consumes({MediaType.TEXT_PLAIN})，下面看看对应的枚举支持的类型：

```java

	public class MediaType {
    
	    private String type;
	    private String subtype;
	
		/** "application/xml" */
	    public final static String APPLICATION_XML = "application/xml";
	
		/** "application/atom+xml" */
	    public final static String APPLICATION_ATOM_XML = "application/atom+xml";
	
		/** "application/xhtml+xml" */
	    public final static String APPLICATION_XHTML_XML = "application/xhtml+xml";
	
		/** "application/svg+xml" */
	    public final static String APPLICATION_SVG_XML = "application/svg+xml";
	
		/** "application/json" */
	    public final static String APPLICATION_JSON = "application/json";
	
		/** "multipart/form-data" */
	    public final static String MULTIPART_FORM_DATA = "multipart/form-data";
	
		/** "application/octet-stream" */
	    public final static String APPLICATION_OCTET_STREAM = "application/octet-stream";
	
		/** "text/plain" */
	    public final static String TEXT_PLAIN = "text/plain";
	
		/** "text/xml" */
	    public final static String TEXT_XML = "text/xml";
	
		/** "text/html" */
	    public final static String TEXT_HTML = "text/html";	
	
		.......
	}

```

> **响应数据类型 @Produces({"application/json"})**

> - 该注解就是用来限定对应的rest接口的返回值类型，相似与springmvc中的@ResponseBody注解的作用，当然该传参形式也可以使用枚举类型，同上面的@Consumes注解使用的枚举传参方式一样


> **响应类型为xml格式的数据封装 @XmlRootElement**

> - 可以看到在我们刚开始定义rest接口数据封装VO类时，在UserVO类上面使用了该注解定义，该注解主要的作用就是制定当rest接口中的响应数据类型为xml格式的UserVO数据时，使用UserVO类名作为根节点，当返回的xml数据集为多条(集合)时，则根节点为类名+es（UserVOes），具体可以看上面的示例；不过我们可以通过其他注解进行具体个性化指定，以后再具体讲解。


> **请求头(header)中传递参数 @HeaderParam("md5val")**

> - 该注解主要用来封装在调用rest接口时，需要在请求头(header)中传递参数的作用，一般当对应的rest接口需要进行一些认证校验处理时，一般都把对应的校验参数通过header传递，该注解等同于springmvc中的@RequestHeader("md5val")的作用。


到目前为止，对应的常规的rest接口的开发就写完了，然后针对开发过程中用到的注解及使用场景进行了具体描述。
