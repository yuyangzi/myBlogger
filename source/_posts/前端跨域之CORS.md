---
title: 前端跨域之CORS
date: 2017-6-21 01:37:12
tags:
- AJAX
- 跨域
categories:
- 原创
- 前端技术
description: 在前端开发中出于种种原因,总会有需要访问其它域名数据的时候.比如说,因为某项业务你需要调用一下第三方的一个接口.或者说你需要与其他域名的网站交互一下数据等等.不一而足.但是,与此同时我们也知道.浏览器在出于安全方面的考虑,有一个同源策略.只要是不同域名,不同端口,不同协议之间的通讯都会被浏览器阻止.而在这种情况下,跨域这个概念也就油然而生了.
---
&emsp;&emsp;在前端开发中出于种种原因,总会有需要访问其它域名数据的时候.比如说,因为某项业务你需要调用一下第三方的一个接口.或者说你需要与其他域名的网站交互一下数据等等.不一而足.但是,与此同时我们也知道.浏览器在出于安全方面的考虑,有一个同源策略.只要是不同域名,不同端口,不同协议之间的通讯都会被浏览器阻止.而在这个情况下,跨域这个概念也就油然而生了.

## 什么是跨域
> 跨域一词从字面意思看，就是跨域名嘛，但实际上跨域的范围绝对不止那么狭隘。具体概念如下：只要协议、域名、端口有任何一个不同，都被当作是不同的域。之所以会产生跨域这个问题呢，其实也很容易想明白，要是随便引用外部文件，不同标签下的页面引用类似的彼此的文件，浏览器很容易懵逼的，安全也得不到保障了就。什么事，都是安全第一嘛。但在安全限制的同时也给注入iframe或是ajax应用上带来了不少麻烦。所以我们要通过一些方法使本域的js能够操作其他域的页面对象或者使其他域的js能操作本域的页面对象（iframe之间）。下面是具体的跨域情况详解：

| URL|说明 | 是否允许通信 |
| ------| ------ | ------ |
| http://www.a.com/a.js |  |
| http://www.a.com/b.js | 同一域名下 | 允许|
| http://www.a.com/lab/a.js |
| http://www.a.com/script/b.js | 同一域名下不同文件夹 | 允许|
| http://www.a.com:8000/a.js | |
| http://www.a.com/b.js | 同一域名，不同端口 | 不允许 |
| http://www.a.com/a.js | |
| https://www.a.com/b.js | 同一域名，| 不同协议 不允许 |
| http://www.a.com/a.js | |
| http://70.32.92.74/b.js | 域名和域名对应ip | 不允许 |
| http://www.a.com/a.js | |
| http://script.a.com/b.js | 主域相同，子域不同 | 不允许（cookie这种情况下也不允许访问）|
| http://www.a.com/a.js |
| http://a.com/b.js | 同一域名，不同二级域名（同上）| 不允许（cookie这种情况下也不允许访问）
| http://www.cnblogs.com/a.js | |
| http://www.a.com/b.js | 不同域名 | 不允许 |

所以简单来说,跨域就是为了避开浏览器的同源策略而使用的一种手段.

## CORS
&emsp;&emsp;就目前而言,常见的跨域方式有三种,一种是服务器代理,一种是JSONP,还有一种就是我们今天要讲的```CORS```.

&emsp;&emsp;```CORS```也被称为跨域资源共享.他是HTML5推出的对于跨域问题的一个解决方案.也是最正统的解决方案.它几乎不需要我们在配置什么,与普通的```XMLHttorequest```请求一样.浏览器在我们发送请求的时候会自动的为我们配置好.只要在服务端支持```CORS```就可以正常的跨域访问.

&emsp;&emsp;在```CORS```之前.所使用的服务器代理和JSONP都是不是标准所支持的.而是我们充满智慧的程序员们另辟蹊径所想出来的解决方案.

### 什么是CORS
> CORS是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）。它允许浏览器向跨源服务器，发出```XMLHttpRequest```请求，从而克服了AJAX只能同源使用的限制。

### CORS简介
> CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。  
整个```CORS```通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。  
因此，实现```CORS```通信的关键是服务器。只要服务器实现了```CORS```接口，就可以跨源通信。

### 两种请求

&emsp;&emsp;在支持```CORS```的浏览器中,```CORS```请求分为两种.

* 一种是不会触发```CORS```预检的简单请求:
> 这些跨域请求与浏览器发出的其他跨域请求并无二致。如果服务器未返回正确的响应首部，则请求方不会收到任何数据。因此，那些不允许跨域请求的网站无需为这一新的 HTTP 访问控制特性担心。

只要同时满足以下两大条件，就属于简单请求

```http
1. 请求方法是以下三种方法之一：
HEAD
GET
POST

2. HTTP的头信息不超出以下几种字段：
Accept
Accept-Language
Content-Language
Last-Event-ID
Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
```

* 一种是会触发```CORS```预检的预检请求
> 与简单请求不同，“需预检的请求”要求必须首先使用 OPTIONS   方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。"预检请求“的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

只要请求满足下述任一条件时，即为预检请求

* 使用了下面任一 HTTP 方法：
  * PUT
  * DELETE
  * CONNECT
  * OPTIONS
  * TRACE
  * PATCH
* 人为设置了对 CORS 安全的首部字段集合之外的其他首部字段。该集合为：
  * Accept
  * Accept-Language
  * Content-Language
  * Content-Type (but note the additional requirements below)
  * DPR
  * Downlink
  * Save-Data
  * Viewport-Width
  * Width
* Content-Type 的值不属于下列之一:
  * application/x-www-form-urlencoded
  * multipart/form-data
  * text/plain

### 服务器设置
* Access-Control-Allow-Origin（必含） – 允许的域名，只能填通配符或者单域名
* Access-Control-Allow-Methods（必含） – 这允许跨域请求的http方法（常见有POST、GET、OPTIONS）
* Access-Control-Allow-Headers（当预请求中包含Access-Control-Request-Headers时必须包含） – 这是对预请求当中Access-Control-Request-Headers的回复，和上面一样是以逗号分隔的列表，可以返回所有支持的头部。
* Access-Control-Allow-Credentials（可选） – 该项标志着请求当中是否包含cookies信息，只有一个可选值：true（必为小写）。如果不包含cookies，请略去该项，而不是填写false。这一项与XmlHttpRequest2对象当中的withCredentials属性应保持一致，即withCredentials为true时该项也为true；withCredentials为false时，省略该项不写。反之则导致请求失败。
* Access-Control-Max-Age（可选） – 以秒为单位的缓存时间。预请求的的发送并非免费午餐，允许时应当尽可能缓存。


### CORS的优势
CORS与JSONP的使用目的相同，但是比JSONP更强大。
JSONP只支持GET请求，CORS支持所有类型的HTTP请求。JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

## 浏览器兼容性

![这里写图片描述](http://img.blog.csdn.net/20170719223605267?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**参考资料**
* [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
* [HTTP访问控制（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
