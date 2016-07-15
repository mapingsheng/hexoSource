---
title: java通过Stream对list集合分组
date: 2016-07-15 14:00:11
categories: java8
tags: Stream
---
现在有一个List集合，想对该集合中的数据分组处理，想到java8中的stream，就搞来试试，非常给力！例子如下

 >User.java

	package com.maps;

    public class User{
    	private Integer id;
		private String type;
		private String name; 
		
		public User(){}
		public User(Integer id,String type,String name){
			this.id = id;
			this.type = type;
			this.name = name;
		}   

		public void setId(Integer id){
			this.id = id;
		}

		public Integer getId(){
			return id;
		}

		public void setType(String type){
			this.type = type;
		}
		
		public String getType(){
			return type;
		}
		
		public void setName(String name){
			this.name = name;
		}
		
		public String getName(){
			return name;
		}
    }

 >MainUser.java

	package com.maps;

	import java.util.ArrayList;
	import java.util.List;
	import java.util.Map;
	import java.util.stream.Collectors;

    public class MainUser{
    	
		public static void main(String[] args){
			List<User> list = getUserList();
			Map<String,List<User>> userGroupMap = list.stream().collect(Collectors.groupingBy(DictValue::getType));
		}


		public static List<User> getUserList(){
			User user1 = new User(1,"张三","小学");
			User user2 = new User(2,"李四","小学");
			User user3 = new User(3,"王五","初中");
			User user4 = new User(4,"马六","高中");
			
			List<User> list = new ArrayList<User>();
			list.add(user1);
			list.add(user2);
			list.add(user3);
			list.add(user4);
				
			return list;
		}
    }

  >运行上面例子得到下面的结果
    
    {高中=[com.maps.User@448139f0], 初中=[com.maps.User@7cca494b], 小学=[com.maps.User@7ba4f24f, com.maps.User@3b9a45b3]}

  >是不是感觉不用for循环挺爽！