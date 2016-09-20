---
title: spring3.1.1入门讲解一(非注解篇)
date: 2016-09-12 13:43:45
categories: spring
tags: spring
---
在qq群里面有很多童鞋经常提出一些问题，我搭建的spring框架示例怎么启动不起来？spring入门示例谁有？等等问题。所以就写了这篇文章，绝对手把手讲解啊！

### 一、准备

1、spring版本：spring-framework-3.1.1.RELEASE

2、jar包：spring包下面的dist文件夹下的所有jar包

![](http://soujava.com/images/spring1.jpg)

3、第三方jar包：
	jsp标签包：jstl-api-1.2.jar、standard.jar

	日志包：commons-logging-1.0.4.jar

### 二、环境搭建

1、新建Dynamic Web Project项目：spring

![](http://soujava.com/images/spring2.png)
	
2、在web.xml文件中添加如下配置
```java
	 <servlet>  
      <servlet-name>spring</servlet-name>  
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
      <init-param>  
	  	<description>加载/WEB-INF/spring-mvc/目录下的所有XML作为Spring MVC的配置文件</description>  
	  	<param-name>contextConfigLocation</param-name>  
	  	<param-value>/WEB-INF/spring-mvc*.xml</param-value>  
	  </init-param>  
      	<!-- load-on-startup：表示启动容器时初始化该Servlet -->  
      	<load-on-startup>1</load-on-startup>  
  	</servlet>  
  	<servlet-mapping>  
      <servlet-name>spring</servlet-name>  
      <!-- url-pattern：表示哪些请求交给Spring Web MVC处理， "/"是用来定义默认servlet映射的。也可以如"*.action"表示拦截所有以action为扩展名的请求。 -->  
      <url-pattern>*.action</url-pattern>  
  	</servlet-mapping>  
```
3、在WEB-INF目录下面创建spring-mvc.xml文件：（这里的文件需要在web.xml文件中进行配置的，详见第2步）
```java
	<?xml version="1.0" encoding="UTF-8"?>  
	<beans xmlns="http://www.springframework.org/schema/beans"  
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
	    xmlns:context="http://www.springframework.org/schema/context"  
	    xmlns:mvc="http://www.springframework.org/schema/mvc"  
	    xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd  
	        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd  
	        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">  
   
    	<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping" />  
  
	    <!-- 表示所有实现了org.springframework.web.servlet.mvc.Controller接口的Bean可以作为Spring Web MVC中的处理器。如果需要其他类型的处理器可以通过实现HadlerAdapter来解决。-->  
	    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter" />  
	      
	     <!-- InternalResourceViewResolver：用于支持Servlet、JSP视图解析；  
	         viewClass：JstlView表示JSP模板页面需要使用JSTL标签库，classpath中必须包含jstl的相关jar包；  
	         prefix和suffix：查找视图页面的前缀和后缀（前缀[逻辑视图名]后缀），比如传进来的逻辑视图名为hello，则该jsp视图页面应该存放在“view/hello.jsp”； -->
		 <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">  
	        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />  
	        <property name="prefix" value="/view/" />  
	        <property name="suffix" value=".jsp" />  
	    </bean>

		<bean name="/hello" class="com.spring.mvc.HelloWorldController"/>  
		<!--注意这里的类路径不要错了，如果不用注解的话，那么每当新建一个controller类就需要在这里配置一下 --> 
  
</beans>  
```

4、在src下面添加controller类：HelloWorldController.java
```java
	package com.spring.mvc;  
  
	import javax.servlet.http.HttpServletRequest;  
	import javax.servlet.http.HttpServletResponse;  
	  
	import org.springframework.web.context.request.RequestContextHolder;  
	import org.springframework.web.context.request.ServletRequestAttributes;  
	import org.springframework.web.servlet.ModelAndView;  
	import org.springframework.web.servlet.mvc.Controller;  
	  
	  
	public class HelloWorldController implements Controller {  
       
	    public ModelAndView handleRequest(HttpServletRequest arg0, HttpServletResponse arg1) throws Exception {  
	          
	       HttpServletRequest request = ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();  
	
	        ModelAndView mv = new ModelAndView();  
	        // 添加模型数据，可以是任意的POJO对象  
	        mv.addObject("message", "Hello World!");  
	        // 设置逻辑视图名，视图解析器会根据该名字解析到具体的视图页面,即WebContent/view/hello.jsp  
	        mv.setViewName("hello");  
	        return mv;  
	    }
  }
```

4、在WebContent/view/目录下面添加hello.jsp页面
```java
	<%@ page language="java" contentType="text/html; charset=utf-8"  
    pageEncoding="utf-8"%>  
	<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>  
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">  
	<html>  
	    <head>  
	        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
	        <title>Insert title here</title>  
	    </head>  
	    <body>  
	        ${message}  
	    </body>  
	</html>
```
5、启动tomcat服务器，访问一下

![](http://soujava.com/images/spring3.jpg)

注：我们的访问url中包括了/hello.action；当以.action为后缀的访问路径都将被DispatcherServlet拦截过滤（web.xml文件中已经配置），然后/hello前缀将被映射到HelloWorldController控制器处理（spring-mvc.xml文件中已经配置）