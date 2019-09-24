---
title: WEB应用内容安全策略(Content Security Policy)
date: 2019-2-20 23:24:12
tags:
- HTTP
- 安全
categories:
- 原创
- 前端技术
description: 内容安全策略(Content Security Policy)简称CSP是由W3C小组定义的一项规范,其主要作用是提供一个额外的安全层,用于检测并削弱某些特定类型的攻击,包括跨站脚本 (XSS) 和数据注入攻击等.
---

# WEB应用内容安全策略(Content Security Policy)

## 内容安全策略(Content Security Policy)是什么?

&emsp;&emsp;[内容安全策略(Content Security Policy)](https://www.w3.org/TR/CSP2/)简称`CSP`是由[W3C](https://www.w3.org/)小组定义的一项规范,其主要作用是提供一个额外的安全层,用于检测并削弱某些特定类型的攻击,包括跨站脚本 (XSS) 和数据注入攻击等.

&emsp;&emsp;目前内容安全策略(Content Security Policy)的规范一共有三个版本:

1. [Content Security Policy Level 1](https://www.w3.org/TR/CSP1/)
2. [Content Security Policy Level 2](https://www.w3.org/TR/CSP2/)
3. [Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)

&emsp;&emsp;现在主要使用的是第二个版本,第三个版本目前还在草案当中

## CSP的作用

&emsp;&emsp;`CSP`被设计出来的目的就是为了**效防范内容注入攻击**,如XSS攻击等.

&emsp;&emsp;它通过让开发者对自己WEB应用声明一个外部资源加载的白名单,使得客户端在运行WEB应用时对外部资源的加载做出筛选和识别,只加载被允许的网站资源.对于不被允许的网站资源不予加载和执行.同时,还可以将WEB应用中出现的不被允许的资源链接和详情报告给我们指定的网址.如此,大大增强了WEB应用的安全性.使得攻击者即使发现了漏洞,也没法注入脚本,除非还控制了一台列入了白名单的服务器.

## 使用CSP

### CSP的选择

&emsp;&emsp;根据W3C的设计,`CSP`分为两种模式:

* 一种是`Content-Security-Policy`.

使用这种模式,将会直接阻止非法的外部资源加载,同时也可以选择是否配置将非法资源加载的链接和行为报告给我们指定的网址

* 另一种是`Content-Security-Policy-Report-Only`

使用这种模式时,客户端在遇到非法的外部资源加载时并不会阻止,而是正常加载.但是会将次加载行为和链接报告给我我指定的网址.所以使用此模式时,必须要使用`report-uri`策略配置报告非法资源加载情况的网址.

&emsp;&emsp;以上是`CSP`策略的两种模式,在实际使用中,我们可以根据自己的情况任意选择其中一种模式.

### CSP的使用方式

&emsp;&emsp;`CSP`的使用方式有两种:

* 一种是前端开发时直接在HTML页面中使用`<meta>`标签,如下:

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self';">

<!-- or -->

<meta http-equiv="content-security-policy-report-only" content="default-src 'self';">
```

* 一种是后端开发或服务运维人员对页面的HTTP请求的响应配置`Content-Security-Policy`属性,如在NGINX服务器上配置如下

```NGINX
add_header  Content-Security-Policy  "default-src 'self'";

# or

add_header  Content-Security-Policy-Report-Only  "default-src 'self'";
```

&emsp;&emsp;通过以上两种方式的任意一种即可启用`CSP`.

### CSP的配置

> &emsp;&emsp;一个策略由一系列策略指令所组成，每个策略指令都描述了一个针对某个特定类型资源以及生效范围的策略。你的策略应当包含一个`default-src`策略指令，在其他资源类型没有符合自己的策略时应用该策略(有关完整列表查看[default-src](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy__by_cnvoid/default-src))。一个策略可以包含`default-src`或者`script-src`指令来防止内联脚本运行, 并杜绝`eval()`的使用。 一个策略也可包含一个`default-src`或 `style-src`指令去限制来自一个`<style>`元素或者style属性的內联样式。

&emsp;&emsp;**示例: 常见用例,来自[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP):**

1. 一个网站管理者想要所有内容均来自站点的同一个源 (不包括其子域名)

```HTTP
Content-Security-Policy: default-src 'self'
```

2. 一个网站管理者允许内容来自信任的域名及其子域名 (域名不必须与CSP设置所在的域名相同)

```HTTP
Content-Security-Policy: default-src 'self' *.trusted.com
```

3. 一个网站管理者允许网页应用的用户在他们自己的内容中包含来自任何源的图片, 但是限制音频或视频需从信任的资源提供者(获得)，所有脚本必须从特定主机服务器获取可信的代码.

```HTTP
Content-Security-Policy: default-src 'self'; img-src *; media-src media1.com media2.com; script-src userscripts.example.com
```

&emsp;&emsp;在这里，各种内容默认仅允许从文档所在的源获取, 但存在如下例外:

> * 图片可以从任何地方加载(注意 "*" 通配符)。
> * 多媒体文件仅允许从 media1.com 和 media2.com 加载(不允许从这些站点的子域名)。
> * 可运行脚本仅允许来自于 userscripts.example.com。

4. 一个线上银行网站的管理者想要确保网站的所有内容都要通过SSL方式获取，以避免攻击者窃听用户发出的请求。

```HTTP
Content-Security-Policy: default-src https://onlinebanking.jumbobank.com
```

&emsp;&emsp;该服务器仅允许通过HTTPS方式并仅从onlinebanking.jumbobank.com域名来访问文档。

5. 一个在线邮箱的管理者想要允许在邮件里包含HTML，同样图片允许从任何地方加载，但不允许JavaScript或者其他潜在的危险内容(从任意位置加载)。

```HTTP
Content-Security-Policy: default-src 'self' *.mailsite.com; img-src *
```

&emsp;&emsp;注意这个示例并未指定`script-src`。在此CSP示例中，站点通过`default-src`指令的对其进行配置，这也同样意味着脚本文件仅允许从原始服务器获取

6. 违例报告样本

&emsp;&emsp;默认情况下，违规报告并不会发送。为启用发送违规报告，你需要指定`report-uri`策略指令，并提供至少一个URI地址去递交报告。这个地址可以是相对于当前网站的相对地址，也可以是一个绝对地址：

```HTTP
Content-Security-Policy: default-src 'none'; style-src cdn.example.com; report-uri /_/csp-reports
```

&emsp;&emsp;报告格式如下:

```json
{
  "csp-report": {
    "document-uri": "http://example.com/signup.html",
    "referrer": "",
    "blocked-uri": "http://example.com/css/style.css",
    "violated-directive": "style-src cdn.example.com",
    "original-policy": "default-src 'none'; style-src cdn.example.com; report-uri /_/csp-reports"
  }
}
```

&emsp;&emsp;违例报告的语法:

>作为报告的JSON对象报告包含了以下数据：
>* `document-uri`: 发生违规的文档的URI。
>* `referrer`: 违规发生处的文档引用（地址）。
>* `blocked-uri`: 被CSP阻止的资源URI。如果被阻止的URI来自不同的源而非文档URI，那么被阻止的资源URI会被删减，仅保留协议，主机和端口号。
>* `violated-directive`: 违反的策略名称。
>* `original-policy`: 在 Content-Security-Policy HTTP 头部中指明的原始策略。


### CSP的策略指令

* 常见策略类型

| 策略指令 | 策略说明|
| ------| ------ |
| default-src | 默认加载策略 |
| script-src| 外部脚本 |
| style-src| 样式表 |
| img-src| 图像 |
| media-src| 媒体文件（音频和视频）|
| font-src| 字体文件 |
| object-src| 插件（比如 Flash）|
| child-src| 框架 |
| frame-ancestors| 嵌入的外部资源（比如`<iframe>`、`<iframe>`、`<embed>`和`<applet>`）|
| connect-src| HTTP 连接（通过 XHR、WebSockets、EventSource等）|
| worker-src| worker脚本 |
| manifest-src| manifest 文件 |

**想要了解策略的全部类型可以查看[这里](https://www.w3.org/TR/CSP2/#directives)**

* 策略值类型

| 指令值 | 指令值说明 |
| ------| ------ |
| * | 允许任何内容 |
| ‘none’ | 不允许任何内容|
| ‘self’|允许来自相同来源的内容（相同的协议、域名和端口）|
| data: | 允许 data: 协议（如 base64 编码的图片）|
| www.Google.com | 允许加载指定域名的资源 |
| *.Google.com | 允许加载 Google.com 任何子域的资源 |
| ‘unsafe-inline’ | 允许使用内联资源,如内联的 `<script>` 元素、javascript: URL、内联的事件处理函数和内联的 `<style>` 元素.两侧单引号是必须的. |
| ‘unsafe-eval’ | 允许使用`eval()`等通过字符串创建代码的方法。两侧单引号是必须的。 |

## 浏览器兼容性

### Content Security Policy Level 1

![CSP1](https://user-gold-cdn.xitu.io/2019/2/20/16906958eb002cb6?w=2354&h=1046&f=png&s=261871)

### Content Security Policy Level 2

![CSP2](https://user-gold-cdn.xitu.io/2019/2/20/1690695d7064cd04?w=2352&h=1166&f=png&s=303992)

上面是我的一些粗浅的总结,希望对大家有所帮助.如果文中有何不当之处请予以斧正,谢谢.

**参考资料:**

* [MDN---CSP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)

* [W3C---Content Security Policy Level 2](https://www.w3.org/TR/CSP2/)
* [阮一峰---Content Security Policy 入门教程](http://www.ruanyifeng.com/blog/2016/09/csp.html)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
