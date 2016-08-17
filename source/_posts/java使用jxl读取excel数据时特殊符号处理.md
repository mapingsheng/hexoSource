---
title: java使用jxl读取excel数据时特殊符号处理
date: 2016-08-17 11:40:01
categories: java
tags: jxl
---

使用JXL读取Excel中数据时，如果Excel中的单元格中有M1.5×6这样的值时，读取数据时，不能识别×，或者为乱码（？）。
 未处理之前代码：
```java
     Workbook modelBook = Workbook.getWorkbook(input);
	 Sheet sheet = modelBook.getSheet(0);
```
处理之后的代码：
```java
     WorkbookSettings setting = new WorkbookSettings();
	 Locale locale = new Locale("zh","CN");
	 setting.setLocale(locale);
	 setting.setEncoding("ISO-8859-1");
	 Workbook modelBook = Workbook.getWorkbook(input,setting);
```
注：处理之后的代码片段中前4行代码段，用来解决乱码问题！

