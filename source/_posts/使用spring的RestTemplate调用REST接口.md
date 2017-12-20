---
title: 使用spring的RestTemplate调用REST接口
date: 2017-12-18 18:18:39
categories: spring
tags: spring
---

开发接口调用时，我们后台一般使用spring的RestTemplate调用对应的REST接口获取数据；具体使用如下：


一、RestTemplate源码中的常用API

```java

	// GET

	@Override
	public <T> T getForObject(String url, Class<T> responseType, Object... urlVariables) throws RestClientException {}

	@Override
	public <T> T getForObject(String url, Class<T> responseType, Map<String, ?> urlVariables) throws RestClientException {}

	@Override
	public <T> T getForObject(URI url, Class<T> responseType) throws RestClientException {}

	@Override
	public <T> ResponseEntity<T> getForEntity(String url, Class<T> responseType, Object... urlVariables)
			throws RestClientException {}

	@Override
	public <T> ResponseEntity<T> getForEntity(String url, Class<T> responseType, Map<String, ?> urlVariables)
			throws RestClientException {}

	@Override
	public <T> ResponseEntity<T> getForEntity(URI url, Class<T> responseType) throws RestClientException {}

>

	// POST

	@Override
	public <T> T postForObject(String url, Object request, Class<T> responseType, Object... uriVariables)
			throws RestClientException {}

	@Override
	public <T> T postForObject(String url, Object request, Class<T> responseType, Map<String, ?> uriVariables)
			throws RestClientException {}

	@Override
	public <T> T postForObject(URI url, Object request, Class<T> responseType) throws RestClientException {}

	@Override
	public <T> ResponseEntity<T> postForEntity(String url, Object request, Class<T> responseType, Object... uriVariables)
			throws RestClientException {}

	@Override
	public <T> ResponseEntity<T> postForEntity(String url, Object request, Class<T> responseType, Map<String, ?> uriVariables)
			throws RestClientException {}

	@Override
	public <T> ResponseEntity<T> postForEntity(URI url, Object request, Class<T> responseType) throws RestClientException {}

>

	// PUT

	@Override
	public void put(String url, Object request, Object... urlVariables) throws RestClientException {}

	@Override
	public void put(String url, Object request, Map<String, ?> urlVariables) throws RestClientException {}

	@Override
	public void put(URI url, Object request) throws RestClientException {}

>

	// DELETE

	@Override
	public void delete(String url, Object... urlVariables) throws RestClientException {
		execute(url, HttpMethod.DELETE, null, null, urlVariables);
	}

	@Override
	public void delete(String url, Map<String, ?> urlVariables) throws RestClientException {
		execute(url, HttpMethod.DELETE, null, null, urlVariables);
	}

	@Override
	public void delete(URI url) throws RestClientException {

>

	// general execution

	@Override
	public <T> T execute(String url, HttpMethod method, RequestCallback requestCallback,
			ResponseExtractor<T> responseExtractor, Object... urlVariables) throws RestClientException {}

	@Override
	public <T> T execute(String url, HttpMethod method, RequestCallback requestCallback,
			ResponseExtractor<T> responseExtractor, Map<String, ?> urlVariables) throws RestClientException {}

	@Override
	public <T> T execute(URI url, HttpMethod method, RequestCallback requestCallback,
			ResponseExtractor<T> responseExtractor) throws RestClientException {}

>

	/**
     * 以上所有的get、post等等方法底层都是调用的该方法执行的rest接口请求
     */
	protected <T> T doExecute(URI url, HttpMethod method, 
		RequestCallback requestCallback,ResponseExtractor<T> responseExtractor) throws RestClientException {}

```

>其实针对上面的GET*、POST*、DELETE*等等rest接口请求方法中，整个调用底层都是调用的doExecute()方法

![Alt text](http://soujava.com/images/restTemplate.png)


二、**RestTemplate调用接口示例**

>1.GET-直接实例化RestTemplate对象并采用GET方法调用对应的rest接口，并返回对应的响应值字符串

```java

	RestTemplate restClient = new RestTemplate();
	String url = "http://localhost:8080/user/info/v5.6.5?id=3f62fca0123";
	String result = restClient.getForObject(new URI(url), String.class);

```

>2.GET-调用对应的REST接口，并用响应结果对象示例接收封装对应的结果集

```java
	
	/**
     * rest接口响应值的数据封装类
     */
	class ResponseResult{
		public ResponseResult(){}
		
		private boolean rlt;
		private String msg;
		private String code;
		private String time;
		private String data;
		
		//get、set方法省略
	}

```


```java

	/**
     * 设置数据传递的超时时间、建立连接的超时时间
     */
    SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
	requestFactory.setReadTimeout(READTIMEOUT);
    requestFactory.setConnectTimeout(CONNECTTIMEOUT);
	
	/**
     * 实例化一个RestTemplate对象
     */
	RestTemplate restClient = new RestTemplate();
	restClient.setRequestFactory(requestFactory);

	/**
     * 用ResponseResult对象封装响应结果
     */
	String url = "http://localhost:8080/user/info/v5.6.5?id=3f62fca0123";
	ResponseResult result = restClient.getForObject(new URI(url), ResponseResult.class);
	System.out.println(result.getMsg());

```

>3.GET-调用对应的REST接口，在rest接口PATH路径中传递参数

```java
	
	/**
     * 请求参数在对应的请求path地址中
     */
	String URI = "http://localhost:8080/user/info/{id}/v5.6.5";
    RestTemplate restTemplate = new RestTemplate();
    ResponseResult result = restTemplate.getForObject(URI, ResponseResult.class, "3f62fca0123");//把请求接口中的{id}参数值放入最后一个入参中，最后一个参数是一个可变长(数组)类型的入参
	System.out.println(result.getMsg());

```

>4.POST-调用对应的REST接口，设置header参数

```java
	
	/**
     * 设置header入参对象
     */
	HttpHeaders headers = new HttpHeaders();
    headers.set("area", "010");
    headers.set("token", "71aee1b842cc49f69d26b4bd980c9b8d"); 
	headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON)); 
	
	/**
     * 把对应的header对象通过HttpEntity进行封装处理
     */
	RestTemplate restClient = new RestTemplate();
	HttpEntity<String> httpEntity = new HttpEntity<String>(headers);
	
	String postUrl = "http://localhost:8080/user/info/v5.6.5?id=3f62fca0123";
	ResponseEntity<ResponseResult> result = restClient.postForEntity(postUrl, httpEntity, ResponseResult.class);

	System.out.println(result.getBody().getMsg());

```

>5.POST-调用rest接口时，传递json参数

*因为一般rest接口都是提供给其他系统调用的，如果一个rest接口的入参数量很多的时候，往往调用比较麻烦，并且容易出错；并且接口提供方一般在对应的rest接口入参中使用对象传参，方便后期接口扩展；所以针对接口调用方来讲，只需要按照入参的对象的属性名称封装一个json格式的数据串即可成功调用，这样一来兼容性会比较好*

```java
	
	/**
     * 设置header入参对象
     */
	HttpHeaders headers = new HttpHeaders();
    headers.set("token", "71aee1b842cc49f69d26b4bd980c9b8d"); 
	headers.setContentType(MediaType.APPLICATION_JSON);
	
	String postUrl = "http://localhost:8080/user/add/v5.6.5";	
	String requestJson = "{\"name\":\"张三\",\"age\":25}";//这里可以用JSONObject处理json格式的入参，避免拼接字符串
	
	/**
     * 把请求入参和对应的header入参封装到HttpEntity对象中
     */
	HttpEntity<String> httpEntity = new HttpEntity<String>(requestJson,headers);
	
	/**
     * 请求对应的rest接口添加用户操作
     */
	RestTemplate restClient = new RestTemplate();
	String result = restClient.postForObject(postUrl, httpEntity, String.class);

```

