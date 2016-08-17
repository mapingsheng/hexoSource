---
title: thymeleaf教程1-环境搭建
date: 2016-08-08 15:16:41
categories: thymeleaf
tags: thymeleaf
---
一、由于springboot这一强大的框架的发布，更由于其采用thymeleaf作为其官方的推荐模板引擎，所以决定学习一下thymeleaf这个模板引擎，当然是通过springboot才了解到的thymeleaf；所以试用也是基于springboot来进行；而springboot采用gradle工具依赖管理，所以要想使用thymeleaf，必须进行如下操作：
1. 在你的springboot项目工程中添加thymeleaf的依赖配置

```java
	 compile("org.springframework.boot:spring-boot-starter-thymeleaf:1.3.3.RELEASE");
```
2. 编写一个测试页面(html),对thymeleaf进行使用
```java
	<!DOCTYPE html>
	<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/web/thymeleaf/layout">
	<head>
	</head>
	<body>
		hello thymeleaf!
	</body>
```
> 我们可以看到这就是一个普普通通的html模板页面，唯一不同的地方就是在<html>标签中添加了一些配置；这就是thymeleaf的标签；这里配置完以后，在下面我们就可以用**<th:XX **这样的标签把数据渲染到html模板中了；

由于该html是模板页面，所以我们不能在浏览器中直接打开浏览thymeleaf渲染数据的效果，需要借助于类似tomcat的web服务器浏览才行！如果仍然直接在浏览器中浏览的话，那么我们看到的效果是<th:XX 标签被忽略的效果！

**二、暂且抛弃springboot，仅仅采用原生态的方式试用thymeleaf，即除了thymeleaf，其它都不包含**

1. eclipse新建maven工程
	![](http://soujava.com/images/newmavenproject.png)
	![](http://soujava.com/images/newmaven1.png)
	![](http://soujava.com/images/newmaven2.png)
	![](http://soujava.com/images/newmaven3.png)
2. 在pom.xml配置对Thymeleaf的依赖
```python

	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  	<modelVersion>4.0.0</modelVersion>
  	<groupId>com.maps</groupId>
  	<artifactId>theymeleaf</artifactId>
  	<packaging>war</packaging>
  	<version>0.0.1-SNAPSHOT</version>
  	<name>theymeleaf Maven Webapp</name>
  	<url>http://maven.apache.org</url>
  
    <repositories>
    <repository>
      <id>sonatype-nexus-snapshots</id>
      <name>Sonatype Nexus Snapshots</name>
      <url>https://oss.sonatype.org/content/repositories/snapshots</url>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
    </repositories>
  
  
    <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    
     <dependency>
      <groupId>org.thymeleaf</groupId>
      <artifactId>thymeleaf</artifactId>
      <version>3.0.1.RELEASE</version>
      <scope>compile</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.6.1</version>
      <scope>compile</scope>
    </dependency>
        
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.6.1</version>
      <scope>compile</scope>
    </dependency>

    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.15</version>
      <scope>compile</scope>
      <exclusions>
        <exclusion>
          <groupId>com.sun.jdmk</groupId>
          <artifactId>jmxtools</artifactId>
        </exclusion>
        <exclusion>
          <groupId>com.sun.jmx</groupId>
          <artifactId>jmxri</artifactId>
        </exclusion>
        <exclusion>
          <groupId>javax.jms</groupId>
          <artifactId>jms</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
  	</dependencies>
  
  
  	<build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
      </resource>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
          <include>**/*.html</include>
        </includes>
      </resource>
    </resources>
        
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.2</version>
        <configuration>
          <source>1.5</source>
          <target>1.5</target>
        </configuration>
      </plugin>
      
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
        <version>2.7</version>
        <configuration>
          <encoding>ISO-8859-1</encoding>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
      </plugin>
    </plugins>
  	</build>
	</project>	
```

3. 中断一下，先讲解一下该demo的执行流程
	![模板引擎执行流程](http://soujava.com/images/thymeleafFollow.png)

4. MyApplication--应用装载器
```java
	package application;

	import java.util.HashMap;
	import java.util.Map;

	import javax.servlet.ServletContext;
	import javax.servlet.http.HttpServletRequest;

	import org.thymeleaf.ITemplateEngine;
	import org.thymeleaf.TemplateEngine;
	import org.thymeleaf.templatemode.TemplateMode;
	import org.thymeleaf.templateresolver.ServletContextTemplateResolver;

	import controller.HomeController;
	import controller.Controller;

	public class MyApplication {

   	 	private TemplateEngine templateEngine;
   	 	private Map<String, Controller> controllersByURL;
    
    	public MyApplication(final ServletContext servletContext) {
        	super();
	        /*1、实例化模板解析器，并配置相关选项*/
	        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(servletContext);
	        /*1.1、其实thymeleaf模板引擎的默认模式是HTML，在这里再次设置只是为了更好理解*/ 
	        templateResolver.setTemplateMode(TemplateMode.HTML);
	        /*1.2、设置模板引擎的默认加载路径*/
	        templateResolver.setPrefix("/WEB-INF/templates/");
	        /*1.3、设置模板引擎的文件后缀*/
	        templateResolver.setSuffix(".html");
	        /*1.4、设置模板缓存时间为1小时，如果不设置，模板将一直在缓存中*/
	        templateResolver.setCacheTTLMs(Long.valueOf(3600000L));
	        /*1.5、模板引擎的默认缓存是开启的，这里设置true是为了更好理解； 其实如果这里设置false的话，当你修改模板后，你就可以在浏览器中实时查看到修改后的模板内容*/
	        templateResolver.setCacheable(false);

	        /*2、实例化模板引擎*/
	        this.templateEngine = new TemplateEngine();
	        /*3、将模板解析器装载到模板引擎中*/
	        this.templateEngine.setTemplateResolver(templateResolver);
	        
	        /*4、针对不同的url实例化不同的业务控制器*/
	        this.controllersByURL = new HashMap<String, Controller>();
	        this.controllersByURL.put("/", new HomeController());
    	}
    
    	public Controller resolveControllerForRequest(final HttpServletRequest request) {
       	 	final String path = getRequestPath(request);
        	return this.controllersByURL.get(path);
    	}
    
    
	    public ITemplateEngine getTemplateEngine() {
	        return this.templateEngine;
	    }

    
	    private static String getRequestPath(final HttpServletRequest request) {
	        String requestURI = request.getRequestURI();
	        final String contextPath = request.getContextPath();
	        
	        final int fragmentIndex = requestURI.indexOf(';'); 
	        if (fragmentIndex != -1) {
	            requestURI = requestURI.substring(0, fragmentIndex);
	        }
	        
	        if (requestURI.startsWith(contextPath)) {
	            return requestURI.substring(contextPath.length());
	        }
	        return requestURI;
	    }
}	
```
5. Controller--控制器接口
```java
	package controller;

	import javax.servlet.ServletContext;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;

	import org.thymeleaf.ITemplateEngine;

	public interface Controller {
		public void process(
            HttpServletRequest request, HttpServletResponse response,
            ServletContext servletContext, ITemplateEngine templateEngine)
            throws Exception;
	}
```
6. HomeController--首页控制器业务类,实现controller接口
```java
	package controller;

	import java.util.Calendar;

	import javax.servlet.ServletContext;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;

	import org.thymeleaf.ITemplateEngine;
	import org.thymeleaf.context.WebContext;

	public class HomeController implements Controller{
	 
	    public HomeController() {
	        super();
	    }
    
    	public void process(
            final HttpServletRequest request, final HttpServletResponse response,
            final ServletContext servletContext, final ITemplateEngine templateEngine)
            throws Exception {
        
        WebContext ctx = new WebContext(request, response, servletContext, request.getLocale());
        ctx.setVariable("today", Calendar.getInstance());//thymeleaf的上下文中设置变量数据
        
        templateEngine.process("home", ctx, response.getWriter());//模板引擎转向到对应的模板页面
   		}
	}

```
7. MyFilter--过滤器类
```java
	package filter;

	import java.io.IOException;

	import javax.servlet.Filter;
	import javax.servlet.FilterChain;
	import javax.servlet.FilterConfig;
	import javax.servlet.ServletContext;
	import javax.servlet.ServletException;
	import javax.servlet.ServletRequest;
	import javax.servlet.ServletResponse;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;

	import org.thymeleaf.ITemplateEngine;

	import application.MyApplication;
	import controller.Controller;
	import entity.User;
	
	public class MyFilter implements Filter{
		private ServletContext servletContext;
    	private MyApplication application;
    
    	public MyFilter() {
    	    super();
    	}
  

    	public void init(final FilterConfig filterConfig) throws ServletException {
        	this.servletContext = filterConfig.getServletContext();
        	this.application = new MyApplication(this.servletContext);
    	}

    	public void doFilter(final ServletRequest request, final ServletResponse response,
            final FilterChain chain) throws IOException, ServletException {
        
        	if (!process((HttpServletRequest)request, (HttpServletResponse)response)) {
            	chain.doFilter(request, response);
        	}
    	}

    	public void destroy() {}

    	private boolean process(HttpServletRequest request, HttpServletResponse response)
            throws ServletException {
        	try {
            	//针对资源请求url进行排除
           	 if (request.getRequestURI().startsWith("/css") ||
                    request.getRequestURI().startsWith("/images") ||
                    request.getRequestURI().startsWith("/favicon")) {
                return false;
           	 }
            
            /*
             * 根据URL映射，获得处理该请求的控制器。
             */
            Controller controller = this.application.resolveControllerForRequest(request);
            if (controller == null) {
                return false;
            }

            /*
             * 获取TemplateEngine(模板引擎)实例
             */
            ITemplateEngine templateEngine = this.application.getTemplateEngine();

            /*
             * 写入响应头部信息
             */
            response.setContentType("text/html;charset=UTF-8");
            response.setHeader("Pragma", "no-cache");
            response.setHeader("Cache-Control", "no-cache");
            response.setDateHeader("Expires", 0);

            /*
             * 执行控制器，并转向到对应的模板视图
             */
            controller.process(request, response, this.servletContext, templateEngine);
            return true;
       	 } catch (Exception e) {
            try {
                response.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
            } catch (final IOException ignored) {
            }
            throw new ServletException(e);
        }
    }	
}

```
8. web.xml配置
```java
	<?xml version="1.0" encoding="UTF-8"?>
	<web-app id="gtvg"
         version="3.1"
         xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">

    <display-name>thymeleafexamples-gtvg</display-name>

    <session-config>
        <session-timeout>60</session-timeout>
    </session-config>

    <filter>
        <filter-name>gtvgfilter</filter-name>
        <filter-class>filter.MyFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>gtvgfilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
</web-app>
```
9. 在webapp/WEB-INF目录下面新建templates(模板目录)

<p><code>因为在MyApplication应用装载器中，我们在初始化模板引擎解析器时，设置了模板的目录选项。
	
	>/*1.2、设置模板引擎的默认加载路径*/
	 templateResolver.setPrefix("/WEB-INF/templates/");

10. 在templates目录下面新建home.html模板文件
```java
	<!DOCTYPE html>
	<html xmlns:th="http://www.thymeleaf.org">
	  <head>
	    <title>Good Thymes Virtual Grocery</title>
	    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
	  </head>
	  <body>
	    <p>hello <span th:text="'Thymeleaf'">word!</span>.</p>
	  </body>
	</html>
```
<p><code>在该模板页面中，我们可以看到使用了 th:text标签显示内容到p标签中

11. 运行pom.xml编译构建完成后，启动tomcat服务器预览测试

	![显示页面](http://soujava.com/images/thyemleafshow.png)

<p><code>根据浏览效果我们可以看到th:text中的内容替换了p标签原有的内容word!



<p><code>该demo已经上传至github上面，https://github.com/mapingsheng/theymeleaf
