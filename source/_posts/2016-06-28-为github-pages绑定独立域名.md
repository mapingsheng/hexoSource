---
title: 为github pages绑定独立域名
date: 2016-06-28 17:47:28
categories: github
tags: github
---
> 如果想让你的博客显得更专业更高逼格，那么申请注册一个你自己的独立一级域名是必须的了，那么下面我接着上一篇博客继续讲一下如何把你的博客跟你的独立域名绑定起来！

**<p>1、假设你已经有了一个独立域名了，至于那些还没有独立域名的童鞋还是先申请注册一个自己的独立域名吧，申请注册一个非常简单，我就不多啰嗦了！我的独立域名是<a href="http://www.soujava.com">soujava</a>；个人感觉这个域名还算可以吧！个人是搞java开发的！**


**<p>2、不管你的域名是在哪里注册的，其实原理通用的，因为我的域名是在新网上面注册的，那么我就以新网为例进行讲解！**
	
- 登陆域名注册服务商提供的域名管理系统进行域名管理-其他网站的界面可能不太一样，但是原理一样就是配置DNS解析你的域名
>![Alt text](http://soujava.com/images/yumingmanage.jpg)
<p>![Alt text](http://soujava.com/images/ippeizhi.jpg)
<p><code>下面讲解此步骤配置的ip如何获取

**<p>3、通过ping命令ping一下你在github上通过gitHub Pages服务生成的博客访问地址**
<p>![Alt text](http://soujava.com/images/getIp.jpg)

**<p>4、将你的域名与ip在DNS上面映射配置完成后，需要等待一段时间，具体多长时间我也不太清楚，当时我是晚上配置的，第二天早上一起床就可以用配置的独立域名访问了！**
<p><code>有些域名服务提供商的DNS解析时间可能不一样，大家可以耐心等候一下！
