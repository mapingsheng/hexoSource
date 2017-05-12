---
title: 通过curl命令调用调试RestApi接口
date: 2017-05-10 16:38:56
categories: java
tags: java
---

昨天仿真环境上面的rest接口突然调不通了，然后就需要到服务器中查看什么原因，后台服务器的物理环境是跳板机-->服务器；也就是说不能直接链接到系统的运行服务器中的，需要先链接到跳板机上面，然后通过跳板机进入系统服务器中，由于系统服务器是linux操作系统，所以就需要在命令行中对一些接口地址进行联调调试操作，就用到了curl命令处理，具体下面分步骤说明其用法。

<!--more-->

**一、语法**

>-X/--request [GET|POST|PUT|DELETE|…]  用于指定请求方式
>
>-H/--header                           设置请求头(header)信息
>
>-i/--include                          请求接口后，输出响应的header信息
>
>-d/--data                             设置请求中的参数 
>
>-v/--verbose                          输出更加详细的响应信息
>

**二、示例调用**

**1. 请求一个普通接口地址**

>curl -i  "http://*******/appteacher/*****/years"

请求接口后输出如下信息：

>HTTP/1.1 200 OK
Server: nginx
Date: Wed, 10 May 2017 09:00:48 GMT
Content-Type: text/html;charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Keep-Alive: timeout=7200
Vary: Accept-Encoding
Set-Cookie: BIGipServerPY_JiaoShiDuan_App_Pool_80=3960187072.20480.0000; path=/
>
>{"rlt":true,"msg":"操作成功","code":"000000","time":"2017-05-10 17:00:48","data":[{"value":2017,"name":"2017年"},{"value":2016,"name":"2016年"},{"value":2015,"name":"2015年"}]}
>

可以看到当我们加入 -i 参数后，header响应信息页输出了；另外如果不加-X 参数设定请求类型，则默认是Get请求方式。

**2. 最简化的请求一个方式为GET的接口**

>curl  "http://*******/appteacher/*****/years"

请求接口后输出如下信息：

>{"rlt":true,"msg":"操作成功","code":"000000","time":"2017-05-10 17:00:48","data":[{"value":2017,"name":"2017年"},{"value":2016,"name":"2016年"},{"value":2015,"name":"2015年"}]}
>

可以看到我们把 -i 参数去除后，就不会输出响应header信息了，输出的仅仅是结果集数据


**3. 请求一个post类型的接口，需要在header中设置参数**

>curl -i -H "area:010" -H "token:2435984u3943" -H "md5:o0priosj9od" -X POST -d "id=544339848ij9s&type=1"  "http://*******/appteacher/*****/order"

注：具体响应就不贴出来了。

- 每一个header参数值都需要用一个 -H标识，多个header参数就用多个-H 进行指定，使用-d参数设定请求中的参数，多个参数可以直接用 & 分割即可；通过-X 命令设置请求方法为POST


**4. 可以在curl命令加上一个 -v 参数，查看更详细的响应信息**

>curl -i -H "area:010" -H "token:2435984u3943" -H "md5:o0priosj9od" -X POST -d "id=544339848ij9s&type=1"  "http://*******/appteacher/*****/order" -v


通过以上命令就可以直接在命令窗口中联调测试restApi接口了！