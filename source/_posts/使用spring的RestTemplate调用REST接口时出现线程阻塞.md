---
title: 使用spring的RestTemplate调用REST接口时出现线程阻塞
date: 2017-12-18 16:32:12
categories: spring
tags: spring
---

由于需要给APP端提供相关的数据服务，所以在后台也少不了调用第三方平台的服务接口，所以开发接口调用时，我们后台一般使用spring的RestTemplate调用对应的REST接口获取数据；最近开发时，由于使用了MappingJackson2HttpMessageConverter转换器，所以导致接口性能压测时，出现了大量的线程BLOCKED：

![Alt text](http://soujava.com/images/threadBlcked.png)


然后通过jstack命令把接口调用过程的线程情况进行了输出，然后根据输出的线程调用栈情况，逐级找到了最终的阻塞原因，整个过程如下：

>1.使用jstack命令输出java的堆栈信息如下：

![Alt text](http://soujava.com/images/dump.png)



>2.根据堆栈信息，逐级进入代码分析步骤如下：

![Alt text](http://soujava.com/images/threaddump.png)



- MappingJackson2HttpMessageConverter类的构造器方法如下

```java
	
	public MappingJackson2HttpMessageConverter() {
		this(Jackson2ObjectMapperBuilder.json().build());
	}

```

- 点击build()方法如下

```java

	public <T extends ObjectMapper> T build() {
		ObjectMapper mapper;
		if (this.createXmlMapper) {
			mapper = (this.defaultUseWrapper == null ? new XmlObjectMapperInitializer().create()
					: new XmlObjectMapperInitializer().create(this.defaultUseWrapper));
		}
		else {
			mapper = new ObjectMapper();
		}
		configure(mapper); //继续点击分析
		return (T) mapper;
	}


```

- 点击configure()方法如下

```java

	public void configure(ObjectMapper objectMapper) {
		Assert.notNull(objectMapper, "ObjectMapper must not be null");

		if (this.findModulesViaServiceLoader) {
			// Jackson 2.2+
			objectMapper.registerModules(ObjectMapper.findModules(this.moduleClassLoader));
		}
		else if (this.findWellKnownModules) {
			registerWellKnownModulesIfAvailable(objectMapper); //继续点击深入分析
		}

		if (this.modules != null) {
			for (Module module : this.modules) {
				// Using Jackson 2.0+ registerModule method, not Jackson 2.2+ registerModules
				objectMapper.registerModule(module);
			}
		}

		....后半部分方法代码省略

```

- 点击进入registerWellKnownModulesIfAvailable()方法如下

```java

	private void registerWellKnownModulesIfAvailable(ObjectMapper objectMapper) {

		  ....前半部分代码省略

		// Joda-Time present?
		if (ClassUtils.isPresent("org.joda.time.LocalDate", this.moduleClassLoader)) {
			try {

				/**
				 * 文件的根源就在这个ClassUtils.forName()方法底层中
				 */
				Class<? extends Module> jodaModule = (Class<? extends Module>)
						ClassUtils.forName("com.fasterxml.jackson.datatype.joda.JodaModule", this.moduleClassLoader);
				objectMapper.registerModule(BeanUtils.instantiate(jodaModule));
			}
			catch (ClassNotFoundException ex) {
				// jackson-datatype-joda not available
			}
		}

		....后半部分代码省略
	}

```


>3.再继续按照整个堆栈信息，逐级向上找到最终的一个spring的java类中【ClassUtils.java:250】

![Alt text](http://soujava.com/images/dumpfenxi.png)

>4.然后进入org.springframework.util.ClassUtils.java类中的第250行

![Alt text](http://soujava.com/images/loadclass.png)

>5.我们点击loadClass()方法，进入到java.lang.ClassLoader.java

```java

	public Class<?> loadClass(String name) throws ClassNotFoundException {
        return loadClass(name, false); //该方法是另一个loadClass方法的重载
    }

```

>6.我们继续点击ClassLoader类中的loadClass()方法，进入到最底层的loadClass方法

![Alt text](http://soujava.com/images/loadclasslock.png)

通过方法我们可以看到该方法中以下两个导致线程阻塞的问题

- 方法中有一个synchronized同步方法
- 同步方法中的getClassLoadingLock方法内部还有一个lock锁

通过排查分析发现，就是上面的问题导致线程BLOCKED；后来去除了MappingJackson2HttpMessageConverter的注册，再次压测时，线程阻塞问题得以解决。


