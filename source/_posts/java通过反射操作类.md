---
title: java通过反射操作类
date: 2017-05-10 11:17:22
categories: java
tags: java
---

在开发中用到反射操作具体类的情况比较少，但是我们用到的一些开发框架确都是反射的应用代表，比如spring、hibernate、mybatis等等框架。

就拿spring框架来讲，我们在application.xml中配置一个bean标签，然后把类的全路径配置好之后，spring启动初始化时就把这个类路径对应的具体类初始化成了一个真正意义上的bean对象，我们就可以直接使用这个bean对象中的方法进行操作了；这里面最典型的就是aop、ioc了。

**一、使用原生反射方式**


<!--more-->


**1. 新建一个普通的java类-Student**

```java

 	public class Student{
		private String userName;
		private String passWord;
		
		public Student(){}
		
		public Student(String userName,String passWord){
			this.userName = userName;
			this.passWord = passWord;
		}
		public String getUserName() {
			return userName;
		}
		public void setUserName(String userName) {
			this.userName = userName;
		}
		public String getPassWord() {
			return passWord;
		}
		public void setPassWord(String passWord) {
			this.passWord = passWord;
		}
		@Override
		public String toString() {
			return "Student [userName=" + userName + ", passWord=" + passWord + "]";
		}
	}

```

**2. 通过反射方法-getDeclaredMethods()获取该类中的全部方法**

 *注：通过getDeclaredMethods获取该类中所有方法，包括公共、保护、默认（包）访问和私有方法，当然不包括其继承的方法(比如Student的隐形继承的类Object中的方法)；通过getMethods获取所有公用（public）方法包括其继承类的公用方法(即包含父类Object中的方法)*

```java
	
	Class<?> clazz = null;
	try {
		clazz = Class.forName("com.maps.test.proxy.Student");
	} catch (ClassNotFoundException e) {
		e.printStackTrace();
	}


	Method[] methods = clazz.getDeclaredMethods();
	for(int i=0;i<methods.length;i++){
		Method method = methods[i];
		
		Parameter[] parameters = method.getParameters();
		StringBuilder sbf = new StringBuilder("(");
		for(int m=0;m<parameters.length;m++){
			Parameter parameter = parameters[m];
			String parameterName = parameter.getName();
			int modify = parameter.getModifiers();
			String parameterModify = Modifier.toString(modify);
			String parameterType = parameter.getType().getSimpleName();
			sbf.append(parameterModify).append(" ").append(parameterType).append(" ").append(parameterName).append(",");
		}
		if(sbf.lastIndexOf(",")>=0){
			sbf.deleteCharAt(sbf.length()-1);	
		}
		sbf.append(")");
		
		System.out.println("[methodInfo:{methodName:"+method.getName()+",returnType:"+method.getReturnType().getName()+"}"+",parameterInfo:{"+sbf.toString()+"}]");
	}

```

以上代码运行结果-输出了每一个方法和方法中的参数的访问权限、类型、名称信息

>[methodInfo:{methodName:toString,returnType:java.lang.String,methodModify:public},parameterInfo:{()}]
[methodInfo:{methodName:setUserName,returnType:void,methodModify:public},parameterInfo:{( String arg0)}]
[methodInfo:{methodName:setPassWord,returnType:void,methodModify:public},parameterInfo:{( String arg0)}]
[methodInfo:{methodName:getPassWord,returnType:java.lang.String,methodModify:public},parameterInfo:{()}]
[methodInfo:{methodName:getUserName,returnType:java.lang.String,methodModify:public},parameterInfo:{()}]


**3. 通过反射方法-getDeclaredFields()获取该类中的所有属性**

*注：getDeclaredFields方法用于获得类中的所有的属性，包括public、private和proteced、default，但是不包括父类中的属性；getFields方法获得类中的所有的公共（public）的字段，包括父类中声明的属性*

```java

	Field[] fields = clazz.getDeclaredFields();
	for(int i=0;i<fields.length;i++){
		Field field = fields[i];
		String fieldName = field.getName();//获取属性的名称
		Class<?> fieldType = field.getType();//获取属性的类型
		String fieldTypeValue = fieldType.getSimpleName(); //获取属性的简单类型，即不带java.lang前缀
		int modify = field.getModifiers();//获取数学的访问权限
		String modifyVal = Modifier.toString(modify);
		System.out.println("[fieldInfo:{fieldModify:"+modifyVal+",fieldType:"+fieldTypeValue+",fieldName:"+fieldName+"}]");
	}

```

以上代码运行结果-输出了Student类中定义的两个属性

>[fieldInfo:{fieldModify:private,fieldType:String,fieldName:userName}]
[fieldInfo:{fieldModify:private,fieldType:String,fieldName:passWord}]

**4. 通过反射调用类中的方法**

```java

	/**
	 * 通过类路径加载类Student
	 */
	Class<?> clazz = null;
	try {
		clazz = Class.forName("com.maps.test.proxy.Student");
	} catch (ClassNotFoundException e) {
		e.printStackTrace();
	}

	try {
		//实例化Studnt对象
		Object obj = clazz.newInstance();
		//获取Student中的方法名为setUserName，参数数量只有一个，参数类型为String的方法体
		Method setUserNameMethod = clazz.getMethod("setUserName",String.class);
		//通过反射invoke调用Student对象中的setUseName方法，并且传递参数“maps”
		setUserNameMethod.invoke(obj,"maps");
		
		/**
		 * 直接修改Student中的属性值
		 */
		Field field = clazz.getDeclaredField("userName");//获取Studnt中名称为userName的属性
	    field.setAccessible(true);//设置该属性的可访问权限
	    field.set(obj, "mapingsheng");//然后把该属性的值设置为“mapingsheng”
		
		/**
		 * 通过反射调用getUserName方法获取上一步设置好的属性-userName的值
		 */
		Method getUserNameMethod = clazz.getMethod("getUserName");
		String userNameValue = (String) getUserNameMethod.invoke(obj);
		System.out.println(userNameValue);
	} catch (Exception e) {
		e.printStackTrace();
	}

```

以上代码运行结果：

>mapingsheng


**二、使用java动态代理-InvocationHandler方式**

代理就是通过中间的代理对象间接的调用真实对象的方法的应用模式，在java中，java.lang.reflect API提供了API提供了proxy类和InvocationHandler接口来实现动态代理操作。Proxy类根据给定的参数创建动态代理类。 InvocationHandler调用动态代理类的方法。 以下将详细讨论所有这些情况：



- java.lang.reflect.Proxy是一个提供静态方法来创建动态代理类的类。Proxy类中有一个newProxyInstance（）方法定义如下：

```java

	public static Object newProxyInstance （ClassLoader loader ，Class <？> [] interfaces ，InvocationHandler h）

```

这个方法中的三个参数的意义：

>ClassLoader：这个类加载器将定义动态代理类。类加载器可以通过正在创建其动态代理的类或接口来获取。
>
>interfaces：第二个参数是被代理的类所实现的所有接口的数组
>
>InvocationHandler：第三个参数是传递实现java.lang.reflect.InvocationHandler的类的实例
>


- java中的InvocationHandler是java.lang.reflect包中的接口。InvocationHandler由用户类实现，以调用动态代理类的方法。invoke方法的语法如下

```java

	Object invoke（Object proxy ，Method m ，Object [] args） 

```

这个方法中的三个参数的意义：

>Object：这是调用方法的代理实例。 
>
>Method：这对应于在代理实例上调用的接口方法。 
>
>Object []：它包含一个在方法调用中传递的参数数组。
>


**- 具体使用代码演示java的动态代理操作**

通过一个结婚的场景来模拟动态代理场景，比如日常生活中要结婚的话，需要预定场地和吃饭的酒店，并且出行需要预定汽车服务。

- 一个代理结构(中介)提供了很多套餐(酒店+出行套餐、酒店+婚礼主持+出行套餐等等)--代理类MarryProxy
- 这个代理机构提供了很多套餐服务，一般开发中都是面向接口编程(MarryPackageService、MarryPackageServiceImpl)

我们采取最经济的套餐（酒店+出行套餐）来讲，可以看到上面场景一共需要提供如下类：

1. 定义一个结婚套餐服务接口

```java

	/**
	 * 结婚套餐(提供酒店+出行服务)
	 * @author mapingsheng
	 */
	public interface MarryPackageService {
		
		/**
		 * 提供酒店服务，一站式解决婚礼举办、就餐问题
		 * @return
		 */
		public String hotel(String address,String hotelName);
		/**
		 * 提供用车服务，解决出行问题
		 * @return
		 */
		public String car(String carName);
	}

```

2. 定义一个结婚套餐接口的实现类

```java

	/**
	 * 结婚套餐(提供酒店+出行服务)具体业务类
	 * @author mapingsheng
	 */
	public class MarryPackageServiceImpl implements MarryPackageService{
	
		@Override
		public String hotel(String address, String hotelName) {
			Date date = new Date();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			
			return sf.format(date)+" 【"+address+" "+hotelName+"】 提供就餐服务";
		}
	
		@Override
		public String car(String carName) {
			return "【"+carName+"】  提供出行服务";
		}
	}

```

3. 定义一个代理结构-代理类

*注：代理类必须实现InvocationHandler接口，我们在构造方法中初始化被代理的对象*

```java

	import java.lang.reflect.InvocationHandler;
	import java.lang.reflect.Method;
	
	public class MarryProxy implements InvocationHandler{
	
		private Object obj;//这个就是我们要代理的真实对象
		
		/**
		 * 通过构造函数初始化代理对象
		 * @param obj
		 */
		public  MarryProxy(Object obj){
			 this.obj = obj;
		 }
		
		/**
		 * 当代理对象调用真实对象的方法时，其会自动的跳转到代理对象关联的handler对象的invoke方法来进行调用
		 */
		@Override
		public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
			return method.invoke(obj, args);
		}
	}

```

4. 新建一个测试类

```java

	import java.lang.reflect.InvocationHandler;
	import java.lang.reflect.Proxy;
	
	public class MarryClient {
	
		public static void main(String[] args) {
			//我们要代理的真实对象
			MarryPackageService marryPackageService = new MarryPackageServiceImpl();
			//初始化代理类
			InvocationHandler handler = new MarryProxy(marryPackageService);
			//通过Proxy的newProxyInstance方法来创建我们的代理对象
			Object obj = Proxy.newProxyInstance(marryPackageService.getClass().getClassLoader(), 
												marryPackageService.getClass().getInterfaces(), 
												handler);
			
			MarryPackageService marryService = (MarryPackageService) obj;
			String hotelInfo = marryService.hotel("北京市海淀区中关村南大街8号", "香格里拉大酒店");
			String carInfo = marryService.car("奥迪A6");
			System.out.println(hotelInfo+" "+carInfo);
		}
	}

```

以上代码运行结果：

>2017-05-10 14:41:57 【北京市海淀区中关村南大街8号 香格里拉大酒店】 提供就餐服务 【奥迪A6】  提供出行服务


