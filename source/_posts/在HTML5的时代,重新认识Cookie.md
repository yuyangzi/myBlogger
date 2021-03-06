---
title: 在HTML5的时代,重新认识Cookie
date: 2017-1-16 17:23:41
tags:
- Cookie
- 本地储存
- HTML5
categories:
- 原创
- 前端技术
description: Cookie是什么?我想任何一个互联网的从业者都会很快的告诉你,它是一种浏览器本地存储的一种方式.可以将一部分资源存放在本地,已达到更好的网络体验.
---

&emsp;&emsp;Cookie是什么?我想任何一个互联网的从业者都会很快的告诉你,它是一种浏览器本地存储的一种方式.可以将一部分资源存放在本地,已达到更好的网络体验.

&emsp;&emsp;然而随着HTML5规范的普及,我们现在似乎有了更好的解决方案.` LocalStorage`和`SessionStorage`.它们是专门为本地存储而诞生的解决方案.和Cookie相比起来.它们也似乎更加优秀:

* 它们有着更好用的API.

&emsp;&emsp;对于Cookie来说它只有一个`document.cookie`这一个API可以使用,不管是读取`Cookie`还是存储`Cookie`你都只能使用它.并且`Cookie`在存储时只能存储一段字符串.所在读取和存储时你都要另外的区封装方法已到达你想要的效果.

&emsp;&emsp;而` LocalStorage`和`SessionStorage`不一样.以` LocalStorage`为例,你存储的时候有`localStorage.setItem()`,你读取的时候有`localStorage.getItem()`,你想要删除的时候有`localStorage.removeItem()`.此外在存储时,它们事以键值对的形式存储的.所以更易于使用.

* 更大的存储空间.

&emsp;&emsp; Cookie的存储大小只有4KB,而` LocalStorage`和`SessionStorage`是5MB.

* 永久的存储时间.

&emsp;&emsp; Cookie的存储是有过期时间的,到期自动删除,而` LocalStorage`是永久存储,除非主动删除.

&emsp;&emsp;经过上述的对比,我们发现,Cookie从任何方面来说都是完败于` LocalStorage`和`SessionStorage`的,或许你会说它在兼容性方面有着优势.但是随着HTML5标准的日益普及.这一点优势也并没有想象中的那么重要.

&emsp;&emsp;那么,既然如此我们是不是就要完全的抛弃Cookie,转投` LocalStorage`和`SessionStorage`的怀抱了.其实并不是.在HTML5的时代,Cookie并不会落伍.依然会具有重要的地位.这是因为`Cookie`的本地存储功能虽然被代替了.但是他的本职工作却依然无可替代.那它的本质工作是什么了 ----***服务器远程在浏览器端存储数据并以此跟踪和识别用户的机制。***

然我们来看看维基百科上对于Cookie的几段描述:
 > &emsp;&emsp;Cookie（复数形态Cookies），中文名称为“小型文本文件”或“小甜饼”[1]，指某些网站为了辨别用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。定义于RFC2109。是网景公司的前雇员卢·蒙特利在1993年3月的发明[2]。

 > &emsp;&emsp;因为HTTP协议是无状态的，即服务器不知道用户上一次做了什么，这严重阻碍了交互式Web应用程序的实现。在典型的网上购物场景中，用户浏览了几个页面，买了一盒饼干和两瓶饮料。最后结帐时，由于HTTP的无状态性，不通过额外的手段，服务器并不知道用户到底买了什么。 所以Cookie就是用来绕开HTTP的无状态性的“额外手段”之一。服务器可以设置或读取Cookies中包含信息，借此维护用户跟服务器会话中的状态。  
&emsp;&emsp;在刚才的购物场景中，当用户选购了第一项商品，服务器在向用户发送网页的同时，还发送了一段Cookie，记录着那项商品的信息。当用户访问另一个页面，浏览器会把Cookie发送给服务器，于是服务器知道他之前选购了什么。用户继续选购饮料，服务器就在原来那段Cookie里追加新的商品信息。结帐时，服务器读取发送来的Cookie就行了。  
&emsp;&emsp;Cookie另一个典型的应用是当登录一个网站时，网站往往会请求用户输入用户名和密码，并且用户可以勾选“下次自动登录”。如果勾选了，那么下次访问同一网站时，用户会发现没输入用户名和密码就已经登录了。这正是因为前一次登录时，服务器发送了包含登录凭据（用户名加密码的某种加密形式）的Cookie到用户的硬盘上。第二次登录时，（如果该Cookie尚未到期）浏览器会发送该Cookie，服务器验证凭据，于是不必输入用户名和密码就让用户登录了。

&emsp;&emsp;从上面维基百科上对`Cookie`描述我们就可以看出,Cookie诞生的原因就是解决辨别用户身份的问题.而本地存储只是实现的一个手段而已.并不是它的主要目的.只是因为,在当时除了`Cookie`之外并没有一套有效的解决本地存储的一个方案.所以`Cookie`就被拿来借用.而借用的时间一长,许多人就以为`Cookie`就是为了实现本地存储的.反而将它的本职功能给忘记了.

&emsp;&emsp;那么,获取有人会问了,` LocalStorage`和`SessionStorage`既然代替了`Cookie`的本地存储功能,那么是否能将`Cookie`的验证功能也一起代替了了.对此,我可以很绝对的告诉你这是不行的.因为服务端可以通过`Cookie`来验证用户,因为浏览器在每次请求的时候都会带上`Cookie`中的信息,这对于最多只能存储4KB大小的`Cookie`来说.这是可以的.但对于有着5MB大小的` LocalStorage`和`SessionStorage`来说是不可想象的.此外,对于`Cookie`服务端可以远程给浏览器端设置,而` LocalStorage`和`SessionStorage`.所以,基于以上两点,使用` LocalStorage`和`SessionStorage`完全代替`Cookie`就目前而言是不可能的.

&emsp;&emsp;当然说了这么多,`Cookie`也并不是没有缺陷.还是借用维基百科上面的资料:

 **Cookie的缺陷**
> 1. Cookie会被附加在每个HTTP请求中，所以无形中增加了流量。
> 2. 由于在HTTP请求中的Cookie是明文传递的，所以安全性成问题。（除非用HTTPS）
> 3. Cookie的大小限制在4KB左右。对于复杂的存储需求来说是不够用的。


**反对Cookies者**
> 一些人反对Cookies在网络中的应用，他们的理由如下：

> 1. 识别不精确
> 2. 识别有时候会发生错误
> 3. 隐私，安全和广告

> &emsp;&emsp;Cookies在某种程度上说已经严重危及用户的隐私和安全。其中的一种方法是：一些公司的高层人员为了某种目的（譬如市场调研）而访问了从未去过的网站（通过搜索引擎查到的），而这些网站包含了一种叫做网页臭虫的图片，该图片透明，且只有一个像素大小（以便隐藏），它们的作用是将所有访问过此页面的计算机写入Cookie。而后，电子商务网站将读取这些Cookie信息，并寻找写入这些Cookie的网站，随即发送包含了针对这个网站的相关产品广告的垃圾邮件给这些高级人员。

**偷窃Cookies和脚本攻击**
> &emsp;&emsp;虽然Cookies没有中电脑病毒那么危险，但它仍包含了一些敏感消息：用户名，电脑名，使用的浏览器和曾经访问的网站。用户不希望这些内容泄漏出去，尤其是当其中还包含有私人信息的时候。  
> &emsp;&emsp;这并非危言耸听，跨站点脚本（Cross site scripting）可以达到此目的。在受到跨站点脚本攻击时，Cookie盗贼和Cookie毒药将窃取内容。一旦Cookie落入攻击者手中，它将会重现其价值。  
> &emsp;&emsp;Cookie盗贼：搜集用户Cookie并发给攻击者的黑客。攻击者将利用Cookie消息通过合法手段进入用户帐户。  
> &emsp;&emsp;Cookie投毒：一般认为，Cookie在储存和传回服务器期间没有被修改过，而攻击者会在Cookie送回服务器之前对其进行修改，达到自己的目的。例如，在一个购物网站的Cookie中包含了顾客应付的款项，攻击者将该值改小，达到少付款的目的。这就是Cookie投毒。


**Cookies的替代品**
> 鉴于Cookie的局限和反对者的声音，有如下一些替代方法：
> 1. Brownie方案，是一项开放源代码工程，由SourceForge发起。Brownie曾被用以共享在不同域中的接入，而Cookies则被构想成单一域中的接入。这项方案已经停止开发。
> 2. P3P，用以让用户获得更多控制个人隐私权利的协议。在浏览网站时，它类似于Cookie。
> 3. 在与服务器传输数据时，通过在地址后面添加唯一查询串，让服务器识别是否合法用户，也可以避免使用Cookie。

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
