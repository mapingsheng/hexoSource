---
title: jersey-1--轻量级Rest接口后台服务框架搭建
date: 2017-07-03 19:28:20
categories: jersey
tags: java
---

随着项目的收尾与上线，忽然感觉到犹如悟空丢掉金箍棒双手释然之感；但这种感觉也犹如隐鼠出洞惶恐至极；或许把一个项目做完之后的反思与感悟可以让我这个大龄coder对这种悚然之感得以慰藉！

<!--more-->

**一、项目背景**

由于手机app端用到了***公司的IM中间件产品，说白了就是在手机中添加一个跟微信聊天一样的功能；该版本app已经上线，但是需要对这个IM中的群组、单聊等活跃度进行数据分析，把聊天数据进行采集，然后存入大数据平台，通过数据分析系统进行分析报表处理。所以迫切需要做一个数据中转服务系统，要求这套系统短小精悍，越轻量级越好，关键是研发周期就5天！！！！

项目框架图

![Alt text](http://soujava.com/images/jerseykuangjiatu.png)


**二、技术调研**


后来经过技术调研，看中了jersey这个rest框架，简单了解试用之后就当机立断的选用了。首先看他的简介：

![Alt text](http://soujava.com/images/jerseyabort.png)

归纳一下就是：

> - 一个开源的RESTful Web服务框架，可以非常简单的开发rest服务，并且支持各种数据类型
>
> - 基于底层的JAX-RS API实现
> 

**三、项目搭建**


**1. 添加依赖**

基于maven新建web工程，然后在pom.xml文件中添加相关的依赖

```java

		<dependency>
		    <groupId>com.sun.jersey</groupId>
		    <artifactId>jersey-core</artifactId>
		    <version>1.19.4</version>
		</dependency>
  	
  		<dependency>
		    <groupId>com.sun.jersey</groupId>
		    <artifactId>jersey-server</artifactId>
		    <version>1.19.4</version>
		</dependency>
		
		<dependency>
		    <groupId>com.sun.jersey</groupId>
		    <artifactId>jersey-servlet</artifactId>
		    <version>1.19.4</version>
		</dependency>
		
		<dependency>
		    <groupId>com.sun.jersey</groupId>
		    <artifactId>jersey-bundle</artifactId>
		    <version>1.19.4</version>
		</dependency>
		
		<dependency>
		    <groupId>com.sun.jersey</groupId>
		    <artifactId>jersey-json</artifactId>
		    <version>1.19.4</version>
		</dependency>

	    <dependency>
	      <groupId>junit</groupId>
	      <artifactId>junit</artifactId>
	      <version>3.8.1</version>
	      <scope>test</scope>
	    </dependency>

```

**2. 新建一个rest接口的数据封装类-HelloWordVO**

```java

	package com.maps.vo;
	
	import java.io.Serializable;
	
	public class HelloWordVO implements Serializable{
		private static final long serialVersionUID = 9129043716825852339L;
		
		private String notice;
	
		public String getNotice() {
			return notice;
		}
	
		public void setNotice(String notice) {
			this.notice = notice;
		}
	}

```


**3. 新建一个rest接口的服务类(控制器)-HelloWordController**

```java

	package com.maps.rest;
	
	import javax.ws.rs.GET;
	import javax.ws.rs.Path;
	import javax.ws.rs.Produces;
	
	import com.maps.vo.HelloWordVO;
	
	@Path("/")
	public class HelloWordController {
		
		@GET
		@Path("/helloWord")
		@Produces({ "application/json" })
		public HelloWordVO helloWord(){
			HelloWordVO helloword = new HelloWordVO();
			helloword.setNotice("helloword");
			return helloword;
		}
	}

```


**4. 配置src/main/webapp/WEB-INF/web.xml**

对于一个web工程来讲，必不可少的就是servlet，说白了，所有的请求响应都是基于servlet处理的，而servlet又需要运行在servlet容器中，所以我们在web.xml中配置servlet容器

```java

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://java.sun.com/xml/ns/javaee  http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
		version="3.0">
	    
	    <servlet>
	    	<!-- 配置servlet容器运行的容器 -->
			<servlet-name>jersey</servlet-name> 
			<servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer</servlet-class>
	        
			<!-- 配置servlet容器初始化的类的路径，就好比spring中的 <context:component-scan base-package="com.*" />的功能 -->
			<init-param>
	            <param-name>com.sun.jersey.config.property.packages</param-name>
				<param-value>com.maps</param-value> 
			</init-param> 
			
			<!-- 开启简单的java对象直接映射转换json的功能，说白了就是可以在相应中直接在方法中对应的的java对象作为响应返回 ，
			就好比spring中的   @RestController的功能 -->
			<init-param>
	            <param-name>com.sun.jersey.api.json.POJOMappingFeature</param-name>
	            <param-value>true</param-value>
	        </init-param>
			
			<load-on-startup>1</load-on-startup>
		</servlet>
	    
	    <servlet-mapping>
	        <servlet-name>jersey</servlet-name>
	        <url-pattern>/*</url-pattern>
	    </servlet-mapping>
	    
	  <display-name>Archetype Created Web Application</display-name>
	</web-app>

```

*到现在为止，我们的rest服务接口已经开发完毕了*


**5. 运行pom.xml编译项目工程**

> clean install package


**4. 启动tomcat服务器,并访问helloWord对应的rest接口**


![Alt text](http://soujava.com/images/jerseyhello.png)


**四、小结**

> 首先搭建一个项目开发框架非常快速简单，几分钟就可以搭建完成，对比用springMvc去搭建的话可想而知
> 
> 对于一个纯净版的工程，整个项目编译完成后的程序文件总大小仅仅3M，而对于一个纯净版的springMVC项目至少要30M，启动该项目仅仅3秒左右，而启动springmvc项目则要10秒以上
> 
> 上面的与springMvc对比不是为了说他不好，主要取决于技术定位与应用场景；当然springmvc可以轻而易举做的事情，jersey就不一定可以






