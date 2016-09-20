---
title: spring3.1.1入门讲解二(注解篇)
date: 2016-09-12 14:00:34
categories: spring
tags: spring
---
在第一篇博客中主要讲解了如何搭建spring框架和运行示例项目，在搭建框架环境的时候，我们需要配置很文件信息；在本篇文章中主要讲解如何采用注解的方式简化开发！

### 一、准备篇-jar包的准备

这里就不详细说明了详见上一篇博客--spring3.1.1入门讲解一（非注解篇）中的准备篇讲解

### 二、环境搭建篇

1、在web.xml文件中添加的配置跟上一篇的配置代码一样，直接复制过去就行了。 

2、配置spring-mvc.xml，重点就在这里，重点配置如下：

	1）如果像我们采用非注解的方式去创建和访问controller控制器时，我们都需要在spring-mvc.xml文件中添加如下配置
	<bean name="/hello" class="com.spring.mvc.HelloWorldController"/>

	2）、那么我们如果采用注解的方式创建和访问controller控制器时，我们就不需要在spring-mvc.xml中配置bean啦；但是需要在spring-mvc.xml添加如下配置：
	<!-- 在com.spring.mvc包下寻找控制器组件 -->      
	<context:component-scan base-package="com.spring.mvc"></context:component-scan>

上面配置的作用就是让spring自动去扫描com.spring.mvc包下面的controller类（当然我们当创建controller后还需要添加@Controller注解来标示）。

3、在src下面创建com.spring.mvc包，然后添加controller类：HelloWorldAnnocationController.java
```java
	package com.spring.mvc;  
	import javax.servlet.http.HttpServletRequest;  
	import org.springframework.stereotype.Controller;  
	import org.springframework.web.bind.annotation.RequestMapping;  
	import org.springframework.web.servlet.ModelAndView;  
	  
	/** 
	 * 页面控制器 
	 *  
	 * @version 1.0 2015-02-11 下午05:39:25 
	 */  
	//使用注解@Controller声明该类是一个控制器       
	@Controller  
	@RequestMapping(value="helloWorldAnnocationController")  
	public class HelloWorldAnnocationController{  
	     //使用注解@RequestMapping声明这个控制器处理index.html的请求       
	      @RequestMapping("/hello")  
	      public ModelAndView helloAnotherWorld( final HttpServletRequest request){     
	          ModelAndView mv = new ModelAndView();  
	            // 添加模型数据，可以是任意的POJO对象  
	            mv.addObject("message", "Hello World!");  
	            // 设置逻辑视图名，视图解析器会根据该名字解析到具体的视图页面，然后就自动从WebContent/view/下面查找hello.jsp视图页面  
	            mv.setViewName("hello");  
	        //将model的数据交给文件名为helloSpringMVC，后缀名为vm（在applicationContext.xml里面设置）的文件显示       
	        return mv;      
	      }  
	}  

```

4、在view/目录下面添加hello.jsp页面
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
5、访问注解版的controller控制器