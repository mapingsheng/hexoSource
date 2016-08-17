---
title: VMwarevSphere添加物理网卡后其中的虚拟机使用该网卡配置
date: 2016-08-17 09:27:45
categories: vSphere
tags: vSphere添加物理网卡
---

一、有一台机架服务器，使用VMwarevSphere进行虚拟化，然后在其上安装N台虚拟机以供不同业务需要，之前该VMwarevSphere只有一个内网的物理网卡，也就是只能内网访问和使用；现在有个需求需要把该VMwarevSphere开放外网功能，并满足其他地域的用户通过公司VPN可以正常使用上面的虚拟机(其中的某一台)。所以现在需要三步操作：
1. 买一个物理网卡插到机架服务器的卡槽上面(哈哈，这一步不用说了，我去趟WC)
2. VMwarevSphereClientk连VMwarevSphere服务器配置网卡
3. 进入到VMwarevSphere中的某一台虚拟机进行网卡配置

二、VMwarevSphereClientk连VMwarevSphere服务器配置网卡
	
1、 插入外网物理网卡之后，我们通过VMwarevSphereClientk连VMwarevSphere服务器后，在“配置”-“网络适配器”中就可以看到刚才新接入的网卡了(如果看不到，则说明你的网卡有问题啊，具体什么问题需要物理解决)，下面的图片中我们可以看到有两个可用的网卡(最下面两个vmnic0、vmnic1，其实vmnic1是我新接入的网卡)，其他的都不可用，仅仅代表支持多少个网卡

![](http://soujava.com/images/vSphere0.jpg)

2、 虽然我们已经接入了物理网卡，但是我们现在系统的网络环境还没有装载进来，我们需要把该网卡添加到网络环境中来；进入“配置”--“网络”，在右侧界面中右上角点击“添加网络”
	
![](http://soujava.com/images/vSphere1.jpg)

3、开始进入到添加网络的界面，其实只需要下一步下一步的进行就可以了

![](http://soujava.com/images/vSphere2.jpg)

4、 选择网卡的网络类型

注：创建交换机那一项，选择网卡时不要被图片中我的选择内容干扰了，选择哪个网卡根据你的实际情况，参照第一幅图片中(你找到你新插入的物理网卡的代号vmnic0~vmnicN)；比如你新接入的网卡是那个你就选择哪个。

![](http://soujava.com/images/vSphere3.jpg)

5、设置网卡的链接方式

![](http://soujava.com/images/vSphere4.jpg)

6、完成配置

![](http://soujava.com/images/vSphere5.jpg)

7、然后再次进入“配置”--“网络”界面中，我们可以看到我们新添加的网卡

![](http://soujava.com/images/vSphere00.jpg)

8、 然后我们对这个网卡进行编辑操作(添加虚拟机端口)

![](http://soujava.com/images/vSphere01.jpg)

![](http://soujava.com/images/vSphere02.jpg)

9、 这里跟上一个步骤差不多，只不过这里的连接类型需要选择“vmKernel”

![](http://soujava.com/images/vSphere03.jpg)

![](http://soujava.com/images/vSphere04.jpg)

10、 配置我们的ip地址(一般是配置你的外网ip地址，因为你既然让他开放到外网，所以你肯定有可能需要在外网通过vmSphereClient进行远程管理吧，这里就是配置你的外网访问ip地址)，到这里我们的物理机和网卡的配置就算完成了，下面需要配置的就是我们需要提供给外网用户访问的虚拟机了。

![](http://soujava.com/images/vSphere05.jpg)

![](http://soujava.com/images/vSphere06.jpg)

11、 选择我们需要提供给外网用户访问的虚“虚拟机”，开始配置网络

![](http://soujava.com/images/vSphere6.jpg)

12、 为虚拟机添加网卡(我们虚拟机要想链接外网，肯定要使用物理服务器的外网网卡，所以我们需要把物理服务器新接入的网卡添加到虚拟机上面)

![](http://soujava.com/images/vSphere7.jpg)

13、 选择网络类型(其实这里又是跟前面的网络配置差不多，多么熟悉的界面)

![](http://soujava.com/images/vSphere8.jpg)

![](http://soujava.com/images/vSphere9.jpg)

14、 然后添加完成后，我们可以看到我们新加入的网络适配器2，这里需要记住右侧的MAC地址，后面的配置网卡配置文件时要用到

![](http://soujava.com/images/vSphere10.jpg)

15、重启一下系统，然后我们进入到需要供外网访问的那台虚拟机中，输入ifconfig命令就可以看到我们为虚拟机添加的网卡了

![](http://soujava.com/images/vSphere11.jpg)



16、当然上一步中看到eth1这个新的网卡还不行，需要为这个网卡配置具体的ip，因为我们既然需要把这台虚拟机提供给外面用户访问，那么ip地址肯定不能老变化，所以我们需要为这个网卡eth1配置相关信息，具体怎么配置可以参考另一篇文章“[Linux双网卡配置(内外网同时访问)](http://soujava.com/Linux%E5%8F%8C%E7%BD%91%E5%8D%A1%E9%85%8D%E7%BD%AE-%E5%86%85%E5%A4%96%E7%BD%91%E5%90%8C%E6%97%B6%E8%AE%BF%E9%97%AE/)”