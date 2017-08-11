---
title: jersey-3--轻量级Rest接口服务开发之入参
date: 2017-07-14 15:48:18
categories: jersey
tags: java
---

本篇文章我主要描述一下通过javax.ws框架开发rest服务接口时，基本的入参操作，主要包括入参类型的全局设定和方法级别设定、参数的默认值等等。

<!--more-->


**一、全局入参设定**

通过在类上面添加注解 *@Consumes(MediaType.类型)* 即可限定全局入参类型的设置，比如 *@Consumes(MediaType.TEXT_PLAIN)*

> 新建控制器类

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


**1. 新建一个方法，方法层级上面不加 *@Consumes* 注解**

```java

	/**
	 * 不设置方法的入参类型，则默认为类级别所注解的@Consumes类型,
	 * 并且方法中的入参没有 @QueryParam
	 * @param userId
	 * @return
	 */
	@GET
	@Path("/paramForClassNoparam")
	public String paramForClassNoparam(String userId){
		return userId;
	}

```

该rest接口发布后，我们分两种情况访问该rest接口

> 访问 http://servername/param/paramForClassNoparam，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码。

----------

> 访问 http://servername/param/paramForClassNoparam?userId=zhangsan，通过方法代码我们可以看到方法有一个String类型的入参userId，所以我们请求该rest接口时，在url中传递这个参数值；请求这个rest接口地址后，方法中的业务代码可以正常运行，但是传递的参数值是无法获取到的，这是因为我们在方法入参中没有通过 *@QueryParam* 指定具体的入参；所以我们上面的rest方法等同于无参数的方法，其实我们可以认为方法中的userId入参是多余的。

小结：在开发rest接口中，如果方法层级上面没有声明注解Consumes，将默认采用全局(类注解)入参注解类型；如果在对应的方法中没有使用注解QueryParam声明具体的参数，那么该参数是毫无意义的，因为请求该接口时将无法获取到对应的传递的参数值。

**2. 新建一个方法，入参前面添加 *@QueryParam***

```java

	/**
	 * 不设置方法的入参类型，则默认为类级别所注解的@Consumes类型
	 * @param userId
	 * @return
	 */
	@GET
	@Path("/paramForClass")
	public String paramForClass(@QueryParam("userId") String userId){
		return userId;
	}

```

该rest接口发布后，我们分两种情况访问该rest接口

> 访问 http://servername/param/paramForClass，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,但是由于我们没有传递对应的userId参数，所以rest接口将不会有任何内容输出，并且响应状态码为204

![Alt text](http://soujava.com/images/paramForClass.png)

----------

> 访问 http://servername/param/paramForClass?userId=zhangsan，该rest接口的请求将会到达对应的方法体中，并且可以正常执行方法体中的业务代码，并且将会输出对应的入参userId传递的具体值。

![Alt text](http://soujava.com/images/paramForClass2.png)

小结：我们在对应的rest接口方法中，通过注解QueryParam指定具体的参数后，如果在请求该rest接口时，不传递对应的参数，那么方法体中获取到的该参数默认为null；如果在请求rest接口时，传递了具体声明的参数，那么方法体中就可以正常获取到该参数的值

**3. 新建一个方法，方法层级声明 *@Consumes* 注解为json类型，入参声明 *@QueryParam***
	
```java

	/**
	 * 设置入参类型为字符串类型，在方法上面的注解@Consumes将覆盖掉类级别的注解@Consumes
	 * 比如类级别的入参类型为字符串类型，而方法级别的入参类型为json类型，所以该方法的入参类型为json类型
	 * @param userId
	 * @return
	 */
	@GET
	@Path("/paramForMethod")
	@Consumes(MediaType.APPLICATION_JSON)
	public String paramForMethod(@QueryParam("userId") String userId){
		return userId; 
	}

```

该rest接口发布后，我们访问该rest接口

> 访问 http://servername/param/paramForMethod?userId=zhangsanfeng，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,并且传递的userId参数值也将直接输出。

小结：我们在方法层级上面使用了注解Consumes指定了入参类型为json，其实该rest方法应该接受json类型的入参才对吧？实际上我们仍然可以正常传递字符串类型的参数userId，所以可以判定通过Consumes注解指定的入参类型并不是必须条件。

**4. 新建一个方法，方法层级声明 *@Consumes* 注解为xml类型，入参声明 *@QueryParam***

```java

	@GET
	@Path("/paramForMethodXml")
	@Consumes(MediaType.APPLICATION_XML)
	public String paramForMethodText(@QueryParam("userId") String userId){
		return userId; 
	}	

```

该rest接口发布后，我们访问该rest接口

> 访问 http://servername/param/paramForMethodXml?userId=zhangsan，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,并且传递的userId参数值也将直接输出。

小结：我们在方法层级上面使用了注解Consumes指定了入参类型为xml,实际上面我们仍然可以传递字符串类型的参数

**5. 新建一个方法，入参通过 *@QueryParam* 注解声明参数名称**

```java

	/**
	 * 在方法的入参中设置入参名称
	 * @param id
	 * @return
	 */
	@GET
	@Path("/paramName")
	public String paramName(@QueryParam("userId") String id){
		return id;
	}

```

该rest接口发布后，我们访问该rest接口

> 访问 http://servername/param/paramName?userId=zhangsan，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,并且传递的userId参数值也将直接输出。

小结：这个rest接口我们通过QueryParam单独声明了入参名称，也就是说通过QueryParam注解声明的参数名称跟方法真实的入参名称是不同的，但是我们通过注解QueryParam声明以后，请求rest接口时，传递的userId参数名称的值就会自动赋值到真实的入参id上面。

**6. 通过注解 *@QueryParam* *@DefaultValue* 为参数声明默认值**

```java

	/**
	 * 为入参设置默认值
	 * @param userId
	 * @return
	 */
	@GET
	@Path("/paramDefultValue")
	public String defaultParamValue(@DefaultValue("123456") @QueryParam("userId") String userId){
		return userId;
	}

```

该rest接口发布后，我们分两种情况访问该rest接口

> 访问 http://servername/param/paramDefultValue，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,虽然由于我们没有传递对应的userId参数，但是通过注解DefaultValue设定默认值后，我们仍然可以下方法体中获取到入参userId的值为123456

----------

> 访问 http://servername/param/paramDefultValue?userId=zhangsan，该rest接口对应的方法体可以正常执行，响应输出zhangsan，因为我们请求rest接口时，传递了userId参数，所以方法体中我们通过注解DefaultValue设定的默认值就被覆盖了。

小结：当我们使用注解DefaultValue声明入参默认值后，当我们请求rest接口时，不传递对应的参数时，方法体中获取的参数值就是我们设定的默认值。

**7. 通过注解 *@QueryParam* 设定后，请求rest接口的入参类型**

```java

	/**
	 * 设置入参的类型
	 * @param userId
	 * @param age
	 * @return
	 */
	@GET
	@Path("paramType")
	public String paramType(@QueryParam("userId") String userId, @QueryParam("age") Integer age){
		return  userId+" "+age;
	}

```

该rest接口发布后，我们分两种情况访问该rest接口

> 访问 http://servername/param/paramType?userId=zhangsan&age=27，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,该请求响应输出zhangsan 27

----------

> 访问 http://servername/param/paramType?userId=zhangsan&age=strs ，报404错误，因为我们的入参age的类型参数值类型传递错误

小结：我们通过 QueryParam 注解声明入参后，请求rest接口时，传递的参数类型需要满足 兼容性；否则将报404错误。

**8. 通过注解 *@QueryParam* 设定后，请求rest接口的入参类型为boolean类型**

```java

	@GET
	@Path("paramTypeForBool")
	public String paramTypeForBool(@QueryParam("flg") Boolean flg, @QueryParam("status") boolean status){
		return  " "+flg+" "+status;
	}

```
该rest接口发布后，我们分三种情况访问该rest接口

> 访问 http://servername/param/paramTypeForBool，该rest接口的请求将会到达对应方法体中，并且可以正常执行方法中的业务代码,只是我们没有传递具体参数值，所以Boolean类型的flg就为null，boolean类型的flg就为false。

----------

> 访问 http://servername/param/paramTypeForBool?flg=str&status=str ，该请求响应输出为 false false。

----------

> 访问 http://servername/param/paramTypeForBool?flg=true&status=true ，该请求响应输出为 true true。

小结：当入参类型布尔类型时，当类型是包装类-Boolean时，如果入参不传的话，接收的为null；当类型不是包装类-boolean时，如果参数不传的话，接收的值为false；当类型不是包装类-boolean时，如果传递的参数值类型为非boolan(不兼容),则方法体中的接收到的值为false。


