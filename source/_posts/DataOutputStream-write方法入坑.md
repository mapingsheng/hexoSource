---
title: DataOutputStream.write方法入坑
date: 2017-12-22 20:47:09
categories: java
tags: java
---

最近需要开发一个web端创建管理具有IM功能的群组，具备跟微信一样的聊天功能，是调用第三方服务的接口实现的；所以调用其他同事的公共方法调用第三方的rest接口时，由于需要带着用户的中文名称注册昵称，所以在调用接口过程中，发现了莫名其妙的中文参数乱码问题，让我很是费解，因为在该设置编码的地方都已经全部设置为utf-8；仍然出现中文乱码。下面是我的排查及解决问题过程：


>整个rest接口调用的公共方法代码大致思路如下

```java

	  String paramter = "name=张三";
      
      String register_account_url = "https://*.wefs.comg/user/register";
	  URL url = new URL(register_account_url);
	  URLConnection connection = url.openConnection();
	  HttpsURLConnection httpsURLConnection = (HttpsURLConnection)connection;
	  httpsURLConnection.setRequestMethod("POST");//设置请求类型
	  httpsURLConnection.setUseCaches(false);
	  httpsURLConnection.setDoInput(true);
	  httpsURLConnection.setDoOutput(true);
	  httpsURLConnection.setConnectTimeout(30000);//设置连接超时时间
	  httpsURLConnection.setReadTimeout(600000);//设置读取超时时间
	  httpsURLConnection.setRequestProperty("Charsert", "UTF-8");//设置编码
	  httpsURLConnection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded;charset=utf-8");
      
	  DataOutputStream submitDos = new DataOutputStream(httpsURLConnection.getOutputStream());

	  submitDos.writeBytes(paramter);//关键的一行代码，这样会出现乱码 
	 
```


----------

- 整个排查过程很是费解，先排查接受web端的参数中是否乱码，然后排查请求第三方接口时是否设置了编码；然后排查向第三方接口传递参数时是否编码统一等等关键点，排查后依然中文乱码

- 就定位到了DataOutputStream.writeBytes(paramter);这一行，因为说实在的，关于这个类这个方法还是第一次见到在这样的场景中使用；

- 然后就进入到writeBytes方法源码中看了一下，果然发现了问题所在，该方法源码如下：

```java

	public final void writeBytes(String s) throws IOException {
        int len = s.length();
        for (int i = 0 ; i < len ; i++) {
            out.write((byte)s.charAt(i));//关键问题就出现在这一行
        }
        incCount(len);
    }

```

我们在源码中发现了一个在for循环中使用了*(byte)s.charAt(i)*这一行代码，下面逐行分解如下：

```java
	
	/**
	 * 我们把上面的for循环中的out.write((byte)s.charAt(i));这一句代码拆开成如下   的for循环语句
	 */
	String s = "张三"；
	int len = s.length();//长度为2
	for (int i = 0 ; i < len ; i++) {
		char cs = s.charAt(i); //张 [一个char占2个字节(16位)，所以可以存储大部分的中文]
    	byte by = (byte)cs; //问题根源所在 [一个byte占1个字节(8位)，把char强转为byte就会发生丢失]
        out.write(by); //其实调用的api方法为 write(int b);只不过把byte自动向上转型为int型而已
     }
	
```

- 解决问题的办法就是不要使用out.writeBytes(String s)这个方法，更改为out.write(byte b[])这个重载的方法

```java

	//有问题的代码 
	submitDos.writeBytes(paramter);

	//更改后的代码
	submitDos.write(paramter.getBytes());

```

>下面针对DataOutputStream的write方法写一个小demo进行验证

```java
	
 	  /**
	   * 声明一个指向D盘下test.txt的输出流(d盘确保已经存在test.txt文件)
	   * 
	   * 然后我们把一个中文字符分别通过out.writeBytes(String s)方法和write(byte b[])写入到这个文件中然后我们查看写入到文件中的内容
	   */
	  DataOutputStream out = new DataOutputStream(new FileOutputStream("D:\\test.txt"));
	  String count = "马";
	  out.writeBytes(count);

```

>上面代码写入到文件中的内容为 l；即英文的消息l字符；因为我们把"马"这个中文调用out.writeBytes(String s)方法时，由于内部发送了如下转换：

```java

 	  char c= count.charAt(0);
	  byte b = (byte)c;//把值为马的char型转换为byte型后，值变为108
	  out.write(c); //由于108对应的就是小写英文字符l

```

所以问题根源就是 JAVA中的char是16位的，一个char存储一个中文字符，直接用转换byte强转换后会变为8位，直接导致高8位丢失，从而导致中文乱码。


```java
	
 	  /**
	   * 声明一个指向D盘下test.txt的输出流(d盘确保已经存在test.txt文件)
	   * 
	   * 然后我们把一个中文字符分别通过out.writeBytes(String s)方法和write(byte b[])写入到这个文件中然后我们查看写入到文件中的内容
	   */
	  DataOutputStream out = new DataOutputStream(new FileOutputStream("D:\\test.txt"));
	  String count = "马";
	  out.write(count.getBytes()); //可以正常的把中文字符 马 输出到test.txt文件中

```

