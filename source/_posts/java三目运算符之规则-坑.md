---
title: java三目运算符之规则(坑)
date: 2017-04-20 19:40:25
categories: java
tags: java
---
平时开发过程中，判断操作除了使用if-else语句之外，三目运算符也经常被使用，然而在使用三目运算符过程中，我们异步都很老实，但是其使用规则则不曾了解，下面就了解一下它的使用规则。

**一、描述**

先了解一下三目运算符的语法：

>表达式1?表达式2:表达式3;

*当表达式1为true时，则执行表达式2；当表达式1为false时，则执行表达式3*

先看下面的一段代码，分析一下执行结果：

```java

	@Test
	public void Test(){
		int i = 60;
		String str1 = String.valueOf(i < 100 ? 80 : 100);
		System.out.println("str1:"+str1);
		
		String str2 = String.valueOf(i < 100 ? 80 : 100.0);
		System.out.println("str2:"+str2);
		
		String str3 = String.valueOf(i < 100 ? 80L : 100.0);
		System.out.println("str2:"+str3);
		
		String str4 = String.valueOf(i < 100 ? "80" : 100.0);
		System.out.println("str2:"+str4);
		
		String str5 = String.valueOf(i < 100 ? 80 : "字符串");
		System.out.println("str2:"+str5);

		String str6 = String.valueOf(i < 100 ? true : 100.06);
		System.out.println("str6:"+str6);
	}

```

运行之后的返回结果如下：

```java
	
	str1:80

	str2:80.0

	str3:80.0

	str4:80

	str5:80

	str6:true

```

**二、分析**

>1、String str1 = String.valueOf(i < 100 ? 80 : 100);

这句代码运行结果为 str1:80,无容置疑，完全正确，也不需要过多解释了。

>2、String str2 = String.valueOf(i < 100 ? 80 : 100.0);

这句代码运行结果为 str2:80.0,看到结果有点纳闷了，因为i=60；并且i<100;所以应该执行表达式2，应该打印出 80 才对啊，怎么会打印出 80.0为浮点数类型呢？
其实三木运算符还有一个规则：

若两个操作数是明确类型的表达式(比如变量)，则按照正常的二进制数字转换，int转为long，long转为浮点型(float、double)等

所以以上代码中，int类型的变量i会被转换为跟100.0同类型(浮点型)；看来三目运算是先执行类型检查->类型转换->执行运算

>3、String str3 = String.valueOf(i < 100 ? 80L : 100.0);

这句代码运行结果为 str3:80.0,此时结合第2行代码的分析可以这么判定，因为i为int型，并且表达式2(80L)中为Long型，理论上i应该会被自动转换为Long型，但是表达式3是浮点型，所以i最终会被转换为浮点型。

>4、String str4 = String.valueOf(i < 100 ? "80" : 100.0);

这句代码运行结果为 str4:80,因为表达式2为字符型，表达式3是浮点型，所以浮点型可以转换为字符型，因此打印的是 str4:80

>5、String str5 = String.valueOf(i < 100 ? 80 : "字符串");

这句代码运行结果为 str5:80；具体原因可以参照第4行代码的分析；但是在第4行代码分析的基础上，还需要再深入分析一下，表达式2和表达式3类型如果不能相互转换呢？其实三目运算符规则就是如果不能类型转换的话，就不做类型转换。

>6、String str6 = String.valueOf(i < 100 ? true : 100.06);

这句代码运行结果为 str6:true，再次验证三目运算符运算时，如果不能类型转换的话，就不做类型转换。

**三、总结**

>如果两个操作数是明确类型的表达式(比如变量)，则按照正常的二进制数字转换规则，int转为long，long转为float/double等

>如果两个操作数表达式类型不能相互转换，则不作转换；比如数值型都可以转换为字符串，但是字符串不能都转换为数值型。

>使用三目运算时，如果使用不当，仅仅会发生自动的类型转换；语句执行流程永远不变。

>在使用三目运算符时，表达式类型务必一致
