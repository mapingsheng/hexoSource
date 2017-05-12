---
title: 设计模式--观察者模式(用java原生Observable、Observer简化观察者模式实现)
date: 2017-05-12 13:10:36
categories: 设计模式
tags: java
---

在上一篇介绍的观察者模式中我们用基本的代码实现了报纸订阅的场景，感觉写了很多代码才实现这个模式，比如在报纸订阅中需要提供订阅、取消订阅、通知订阅者等等通用的方法，在定义观察者时需要提供被通知的通用方法；

<!--more-->

其实在java中已经原生支持了观察者模式的实现，我们通过java原生提供的Observable(类)、Observer(接口)可以很简单的实现观察者模式，比如上面描述的提供订阅、取消订阅、通知订阅者等通用方法他都进行了实现，我们直接调用即可。


**一、Observer接口实现观察者**

**1. 订阅者-张三**

我们只需要把订阅者实现Observer接口，并且实现update方法即可完整定义成一个观察者

```java

	import java.util.Observable;
	import java.util.Observer;
	
	/**
	 * 张三订阅者，实现了Observer接口
	 * @author mapingsheng
	 */
	public class SubscriberZhangsan implements Observer{
	
		Observable observable; //订阅服务超类
		
		/**
		 * 通过观察者的构造函数初始化订阅服务，并且调用订阅服务超类的addObserver方法把当前对象进行注册
		 * @param observable
		 */
		SubscriberZhangsan(Observable observable){
			this.observable = observable;
			observable.addObserver(this);
		}
	
		SubscriberZhangsan(){}
		
		/**
		 * 覆写观察者的update方法，这里的update方法就等同于被通知的方法(notice)
		 * 由于一个观察者可能订阅不同的报纸，所以我们需要对传入的订阅服务类进行类型判断，并且根据类型调用不同的通知方法
		 */
		@Override
		public void update(Observable o, Object arg) {
			if(o instanceof NewsPaperFoodService){
				NewsPaperFoodService  foodService = (NewsPaperFoodService)o;
				System.out.println("wangwu收到通知： "+foodService.getNotice());
			}else if(o instanceof NewsPaperSportService){
				NewsPaperSportService sportService = (NewsPaperSportService) o;
				System.out.println("wangwu收到通知： "+sportService.getNotice());
			}
		}
	}

```


**2. 订阅者-王五**

```java

	import java.util.Observable;
	import java.util.Observer;
	
	/**
	 * 王五订阅者，实现了Observer接口
	 * @author mapingsheng
	 */
	public class SubscriberWangwu implements Observer {
	
		Observable observable; //订阅服务超类
		
		/**
		 * 通过观察者的构造函数初始化订阅服务，并且调用订阅服务超类的addObserver方法把当前对象进行注册
		 * @param observable
		 */
		SubscriberWangwu(Observable observable){
			this.observable = observable;
			observable.addObserver(this);
		}
		
		SubscriberWangwu(){}
		
		/**
		 * 覆写观察者的update方法，这里的update方法就等同于被通知的方法(notice)
		 * 由于一个观察者可能订阅不同的报纸，所以我们需要对传入的订阅服务类进行类型判断，并且根据类型调用不同的通知方法
		 */
		@Override
		public void update(Observable o, Object arg) {
			if(o instanceof NewsPaperFoodService){
				NewsPaperFoodService  foodService = (NewsPaperFoodService)o;
				System.out.println("zhangsan收到通知： "+foodService.getNotice());
			}else if(o instanceof NewsPaperSportService){
				NewsPaperSportService sportService = (NewsPaperSportService) o;
				System.out.println("zhangsan收到通知： "+sportService.getNotice());
			}
		}
	}

```

**二、Observable类实现订阅服务**

**1. 《美食天下》-报纸订阅服务**

```java

	import java.util.Observable;
	import java.util.Observer;
	
	/**
	 * 美食天下-报纸订阅服务，只需继承Observable类即可
	 * @author mapingsheng
	 */
	public class NewsPaperFoodService extends Observable {
		private String pageName; //报纸类型名称
		
		private String notice; //通知短语
		
		/**
		 * 新报纸发布方法
		 * @param name 报纸的名称
		 */
		public void newsPaperPublish(String name){
			this.pageName = name;
			this.notice = "【新报纸发布】您好，我报社最近出版了 《"+pageName+"》 欢迎订阅";
			setChanged();//这里需要注意，当我们需要通知所有订阅者时，需要调用该方法标记对应的状态
			notifyObservers();//调用通知订阅者方法
		}
		/**
		 * 覆写addObserver注册订阅服务
		 */
		@Override
		public synchronized void addObserver(Observer o) {
			super.addObserver(o);
			this.notice = "【订阅回执】您好，恭喜您订阅《天下美食》专栏报纸，一共 52 元，截止目前一共有 "+super.countObservers()+" 人订阅";
			
			o.update(this, notice); //当订阅成功后，调用订阅者的update方法，向该订阅者发送订阅回执
		}
		
		/**
		 * 获取新发布的报纸名称
		 * @return
		 */
		public String getPageName() {
			return pageName;
		}
		
		/**
		 * 设置新发布的报纸名称
		 * @return
		 */
		public void setPageName(String pageName) {
			this.pageName = pageName;
		}
		/**
		 * 设置通知提示短语
		 * @return
		 */
		public void setNotice(String notice) {
			this.notice = notice;
		}
		/**
		 * 获取提示短语
		 * @return
		 */
		public String getNotice(){
			return notice;
		}
	}

```

**2. 《体育速递》-报纸订阅服务**

```java

	import java.util.Observable;
	import java.util.Observer;
	
	/**
	 * 体育速递-报纸订阅服务，只需继承Observable类即可
	 * @author mapingsheng
	 */
	public class NewsPaperSportService extends Observable {
		private String pageName;  //报纸类型名称
		private String notice; //通知短语
		
		/**
		 * 新报纸发布方法
		 * @param name
		 */
		public void newsPaperPublish(String name){
			this.pageName = name;
			setChanged();//这里需要注意，当我们需要通知所有订阅者时，需要调用该方法标记对应的状态
			notifyObservers();
		}
		
		/**
		 * 覆写addObserver注册订阅服务
		 */
		@Override
		public synchronized void addObserver(Observer o) {
			super.addObserver(o);
			this.notice = "【订阅回执】您好，恭喜您订阅《体育速递》专栏报纸，一共 46元，截止目前一共有 "+super.countObservers()+" 人订阅";
			
			o.update(this, notice); //当订阅成功后，调用订阅者的update方法，向该订阅者发送订阅回执
		}
	
		public String getNotice(){
			String str = "【新报纸发布】您好，我报社最近出版了 《"+pageName+"》 欢迎订阅";
			return str;
		}
		
		public String getPageName() {
			return pageName;
		}
	
		public void setPageName(String pageName) {
			this.pageName = pageName;
		}
	
		public void setNotice(String notice) {
			this.notice = notice;
		}
	}

```

**三、测试-观察者主动注册**

```java

	public class Test {
	public static void main(String[] args) {
		//实例化我们的美食天下报纸订阅服务
		NewsPaperFoodService foodService = new NewsPaperFoodService();

		//实例化订阅者张三、王五，并且把美食天下订阅服务传入构造方法中实现自动注册服务
		SubscriberZhangsan zhangsan = new SubscriberZhangsan(foodService);
		SubscriberWangwu wangwu = new SubscriberWangwu(foodService);
		
		//美食天下相关的报纸-食材中国报纸发布出版，然后通知美食天下报纸的订阅者
		foodService.newsPaperPublish("食材中国");
	}
}

```

以上代码运行结果：

>wangwu收到通知： 【订阅回执】您好，恭喜您订阅《天下美食》专栏报纸，一共 52 元，截止目前一共有 1 人订阅
>
>zhangsan收到通知： 【订阅回执】您好，恭喜您订阅《天下美食》专栏报纸，一共 52 元，截止目前一共有 2 人订阅
>
>zhangsan收到通知： 【新报纸发布】您好，我报社最近出版了 《食材中国》 欢迎订阅
>
>wangwu收到通知： 【新报纸发布】您好，我报社最近出版了 《食材中国》 欢迎订阅
>

可以看到，我们通过张三、王五订阅者的构造方法中传入美食天下订阅者服务实现了自动注册(其实我们把addObserver方法写在了构造方法中而已)；然后当新报纸发布时，通知所有的美食订阅者。


**三、测试-观察者被动注册**

```java

	public class Test {
		public static void main(String[] args) {
			//实例化我们的美食天下报纸订阅服务
			NewsPaperFoodService foodService = new NewsPaperFoodService();
			
			/**
			 * 实例化张三、王五订阅者(无参数构造方法)
			 */
			SubscriberZhangsan zhangsan = new SubscriberZhangsan();
			SubscriberWangwu wangwu = new SubscriberWangwu();
			
			/**
			 * 调用订阅者服务的addObserver方法实现注册
			 */
			foodService.addObserver(zhangsan);
			foodService.addObserver(wangwu);
			
			foodService.newsPaperPublish("食材中国");
		}
	}

```

以上代码运行结果：

>wangwu收到通知： 【订阅回执】您好，恭喜您订阅《天下美食》专栏报纸，一共 52 元，截止目前一共有 1 人订阅
>
>zhangsan收到通知： 【订阅回执】您好，恭喜您订阅《天下美食》专栏报纸，一共 52 元，截止目前一共有 2 人订阅
>
>zhangsan收到通知： 【新报纸发布】您好，我报社最近出版了 《食材中国》 欢迎订阅
>
>wangwu收到通知： 【新报纸发布】您好，我报社最近出版了 《食材中国》 欢迎订阅



**四、解读**

1. 订阅服务中调用通知观察者方法之前需要先调用setChanged()方法

```java

	setChanged();//这里需要注意，当我们需要通知所有订阅者时，需要调用该方法标记对应的状态
	notifyObservers();//调用通知所有订阅者的方法

```

如果不提前调用setChanged方法时，则虽然调用了notifyObservers方法去通知所有订阅者，但是这个方法内部并没有真正意义去通知所有的订阅者。下面我们看看两个方法的联系。

>setChanged()

```java
	
	 protected synchronized void setChanged() {
        changed = true;
     }

```

*这个方法主要是设置boolean类型变量changed的值为true，其实这个变量就是一个开关*

>notifyObservers()

```java

	public void notifyObservers(Object arg) {
        Object[] arrLocal;

        synchronized (this) {
			/* 
             * 根据changed值控制是否执行真正意义的通知观察者操作
             */
            if (!changed)
                return;
            arrLocal = obs.toArray(); //把订阅者集合中的数据赋值给待局部遍历数组，以供下面的批量通知
			
            clearChanged();//将changed变量赋值为false
        }
		
		/* 
         * 循环遍历订阅者集合中的数据，并调用订阅者的update方法实现发送通知操作
         */
        for (int i = arrLocal.length-1; i>=0; i--)
            ((Observer)arrLocal[i]).update(this, arg);
    }

```

>addObserver(Observer o)

```java
		
	   /**
		* 首先在Observable类中定义了以下两个变量
		*/
		
	   private boolean changed = false;
       private Vector<Observer> obs;

	    /** 通过构造方法初始化订阅者集合 */
	
	    public Observable() {
	        obs = new Vector<>();
	    }
	
	    /**
	     * 把观察者添加到订阅者集合中
	     */
	    public synchronized void addObserver(Observer o) {
	        if (o == null)
	            throw new NullPointerException();
	        if (!obs.contains(o)) {
	            obs.addElement(o);
	        }
	    }

```

>clearChanged() 

```java
	
	 protected synchronized void clearChanged() {
        changed = false; //将changed变量赋值为false
    }

```

通过java原生支持的类和接口是不是就非常容易的实现了观察者模式

