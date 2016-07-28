---
title: Mybatis动态传入tableName--非预编译(STATEMENT)
date: 2016-07-28 19:12:02
categories: Mybatis
tags: 动态传入tableName
---
在使用Mybatis过程中，你可以体会到它的强大与灵活之处，由衷的为Mybatis之父点上999个赞！在使用过程中经常会遇到这样一种情况，我查询数据的时候，表名称是动态的从程序中传入的，比如我们通过mybatis的xml文件写sql查询时都是下面的样子：
**1、正常的查询**
```java
   <select id="activityEnrollModelTableName" parameterType="java.util.HashMap" resultType="java.util.HashMap">
       SELECT * FROM user WHERE userid = #{userid}
   </select>
```
上面的查询语句用mybatis执行时,其实是自动的按照JDBC的预编译语句方式执行的，等同于下面一段JDBC代码的执行过程
```java
	Class.forName("com.mysql.jdbc.Driver");
	Connection conn = DriverManage.getConnection("jdbc:mysql://localhost:3306/dbname","root","112233");
	PreparedStatement preState = conn.prepareStatement("SELECT * FROM user WHERE userid = ?"); 
	preState.setString(1,"96");
	ResultSet result = preState.executeQuery();
	while(result.next()){
		result.getString(columnname);
		..........
	}	
```
到这里我们不禁疑惑，难道mybatis默认都是按照预编译语句的方式执行sql的吗？其实就是这样。通过查看mybatis官网文档可以看到有这么一个参数，statementType=[STATEMENT | PREPARED | CALLABLE ]；有三个可选值，mybatis默认值是PREPARED；
这个参数是什么作用呢：
1. 设定mybatis执行sql的模式
2. STATEMENT设定为非预编译语句模式
3. PREPARED设定为预编译语句模式--mybatis默认
4. CALLABLE设定为兼容模式，或者自适应模式，比如设置该值后，mybatis处理sql时会自动的处理根据#、$去判断处理，后面说一下#和$的区别。


综上所述,mybatis默认按照预编译语句方式执行sql语句

**2、动态传入表名**

其实也经常会遇到动态的传入tableName的情况，也就是说上面的sql语句中的"user"是动态传入的，动态传入表名是mybatis中的一种特殊情况，
```java
   <select id="activityEnrollModelTableName" parameterType="java.util.HashMap" resultType="java.util.HashMap">
       SELECT * FROM #{user} WHERE userid = #{userid} //错误的写法
   </select>
```
针对上面的语句，如果让mybatis仍然按照预编译语句方式执行时，等同于如下面的JDBC代码：
```java
	Class.forName("com.mysql.jdbc.Driver");
	Connection conn = DriverManage.getConnection("jdbc:mysql://localhost:3306/dbname","root","112233");
	PreparedStatement preState = conn.prepareStatement("SELECT * FROM ? WHERE userid = ?"); 
	preState.setString(1,"USER");
	preState.setString(2,"96");
	ResultSet result = preState.executeQuery();
	while(result.next()){
		result.getString(columnname);
		..........
	}	
```
我们把该段JDBC代码通过java代码执行后，发现会报异常：
```java
   java.sql.SQLException: ORA-00903: 表名无效
```
所以可以说明预编译语句不能用于列名(查询的列名也不能用预编译语句)、表名；只能作用与where条件参数属性！既然JDBC就不能将预编译语句方式作用与表名上面，那么mybatis就同样也行不通(因为mybatis默认是预编译语句模式)。不过mybatis也早已考虑到了这种情况，所以为我们做了处理：
1. select标签语句中添加statementType="STATEMENT"的属性配置
2. 标签内的sql语句中将所有的${}更换成为#{}，即将$还成#;
```java
   <select id="activityEnrollModelTableName" statementType="STATEMENT" parameterType="java.util.HashMap" resultType="java.util.HashMap">
       SELECT * FROM ${user} WHERE userid = ${userid} //正确的写法
   </select>
```
上面的语句标签中通过添加statementType="STATEMENT"配置后，mybatis就不再使用预编译语句方式执行sql语句了，也就是通过直接执行sql语句操作；那么既然添加了statementType="STATEMENT"非预编译配置后，为什么还需要把#换成$呢？其实是这样：
1. "#" 是预编译语句模式下面的默认匹配符，也就是说mybatis遇到#{}时，将#{userid}替换成占位符?;被解析为一个JDBC预编译语句，然后再将#{userid}本身的值set进来。
2. "$" 是非预编译语句下面的匹配符，非预编译语句说白了就是你传入什么sql语句，就执行什么sql语句，mybatis不做任何处理操作，但是这里mybatis会将${}对应的值，当做一个字符串处理，也就是说你程序接口方法中传递过来参数值是什么，对应的sql填充就是什么！