---
title: java和javaScript代码进行交互初探一
date: 2017-04-22 16:34:17
categories: java
tags: java
---
开发这么长时间，一直的开发思想模式就是java负责服务器端(后台)运行，js负责前端(前台)交互体验，两个语言没有任何代码级别的交互；但其实还有另一种开发模式，就是java和javascript混合开发，前后端不那么分离；即java代码可以直接调用javascript文件中定义的函数；javascript中定义的函数可以读取java代码中定义的变量数据；感觉有点意思！其实这种开发模式个人觉得也很有价值，比如下面的场景：

>用户在浏览一个商品，突然这个商品由于某些原因要求进行打折处理，比如打8折，即需要把该商品的价格乘以80%；
>
>因为上面说的打8折不是固定的，有可能一会打8折，一会又打5折，所以需要把这个信息在一个地方维护起来，那么我们就把打折规则封装到javascript文件定义的函数中
>
>java代码获取该商品的价格后，直接调用javascript文件中定义的打折规则函数，重新计算商品价格即可
>

**一、编写javascript文件**

>1、在本地磁盘上新建一个js文件,比如在D:\maps.js
>
>2、在js文件中定义一个商品对应的打折函数

```java
	
	//该函数非常简单，就是把商品价格打8折
	function discount(money){
      return money * 0.8;
    }

```

**二、编写java代码**

>1、新建一个java类文件，名称随便起
> 
>2、在java文件中新建一个main方法，用来写测试代码
>
```java
	
	/**
	 * 商品消费方法
	 * @throws ScriptException 
	 * @throws FileNotFoundException 
	 * @throws NoSuchMethodException 
	 */
	public static void spend(int money) throws FileNotFoundException, ScriptException, NoSuchMethodException{
		//获取javascript执行引擎
		ScriptEngine engine = new ScriptEngineManager().getEngineByName("javascript"); 
        System.out.println("该商品价格："+money);
         //装载待执行的Js代码文件
        engine.eval(new FileReader("D:/maps.js"));
         //先判断是否可调用方法
        if (engine instanceof Invocable) {
             Invocable in = (Invocable) engine;
             // 执行Js中的discount()函数
             Double result = (Double) in.invokeFunction("discount", money);
             System.out.println("打折后的商品价格构是：" + result.intValue());
        }
	}
	
	public static void main(String[] args) throws FileNotFoundException, ScriptException, NoSuchMethodException{
		spend(100);
	}

```

>上面代码输出结果：
>
>该商品价格：100
>
>打折后的商品价格是：80


**三、修改javascript文件**

>1、修改maps.js文件中的打折函数，在函数中新增使用一个java代码中定义的变量
>
```java

	//说白了，就是把js函数中写死的百分比改成了重java代码中获取
	function discount(money){
     	return money * dis;
	}
	
```

**四、修改java代码**

```java

	/**
	 * 商品消费方法
	 * @throws ScriptException 
	 * @throws FileNotFoundException 
	 * @throws NoSuchMethodException 
	 */
	public static void spend(int money) throws FileNotFoundException, ScriptException, NoSuchMethodException{
		//获取javascript执行引擎
		ScriptEngine engine = new ScriptEngineManager().getEngineByName("javascript"); 
		//定义js函数中使用的折扣变量
		Bindings bind = engine.createBindings();
		bind.put("dis", 0.9);
		engine.setBindings(bind, ScriptContext.ENGINE_SCOPE);
        System.out.println("该商品价格："+money);
         //装载待执行的Js代码文件
        engine.eval(new FileReader("D:/maps.js"));
         //先判断是否可调用方法
        if (engine instanceof Invocable) {
             Invocable in = (Invocable) engine;
             // 执行Js中的discount()函数
             Double result = (Double) in.invokeFunction("discount", money);
             System.out.println("打折后的商品价格是：" + result.intValue());
        }
	}
	
	public static void main(String[] args) throws FileNotFoundException, ScriptException, NoSuchMethodException{
		spend(100);
	}


```

>其实上面java代码中只增加了如下代码
>
>Bindings bind = engine.createBindings();//创建一个js执行引擎的数据绑定器
>
>bind.put("dis", 0.9); //定义dis变量值
>
>engine.setBindings(bind, ScriptContext.ENGINE_SCOPE);
> 

上面的例子实现了简单的java代码和js代码交互功能，这样其实我们可以实现另一个场景，不需要重启服务器，就可以实时控制扩展和变更后台运行的一些灵活的业务规则。
