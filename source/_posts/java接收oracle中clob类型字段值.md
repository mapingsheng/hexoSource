---
title: java接收oracle中clob类型字段值
date: 2016-07-06 16:16:33
categories: java
tags: java接收clob字段
---
oracle数据库中有一种大文本字段类型：clob;那么当我们的java程序从数据库中查询出来的数据列表用java程序如何接收处理呢？
 
    Clob columnContentClob = result.getClob(1); //先用大文本类型接收,getClob方法可以传入列名(columnName)重载使用
    Reader is = columnContentClob.getCharacterStream();//用reader接收
    BufferedReader bf = new BufferedReader(is);//实例化一个缓冲流
    String columnContent = bf.readLine();//读取大文本的内容

**实例**

	String driver = "oracle.jdbc.OracleDriver";
    String url = "jdbc:oracle:thin:@127.0.0.1:1521/databaseName";
    String username = "username";
    String password = "password";
    Class.forName(driver);
    Connection conn = DriverManager.getConnection(url, username, password);

 	ResultSet rs = null;
    PreparedStatement pstmt = null;
    String query = "SELECT description FROM mytable WHERE id = ?";
    try {
      conn = getConnection();
      pstmt = conn.prepareStatement(query);
      pstmt.setInt(1, id);
      rs = pstmt.executeQuery();
      rs.next();
      Clob clob = rs.getClob(1);//rs.getClob("description");
      
    } finally {
      rs.close();
      pstmt.close();
      conn.close();
    }