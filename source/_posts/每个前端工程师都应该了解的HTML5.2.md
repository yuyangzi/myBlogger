---
title: 每个前端工程师都应该了解的HTML5.2
date: 2018-1-9 23:13:34
tags:
- HTML5
categories:
- 原创
- 前端技术
description: 一般在W3C当中.对版本的发布会经过四个阶段分别是:工作草案（WD）、备选推荐标准（CR）、提案推荐标准（PR）和最终的W3C正式推荐标准（REC）.而当一个规范到达REC阶段时，就意味着它已经得到了W3C成员的正式认可，并推荐它由用户代理部署，前端工程师进行使用。
---
# HTML5.2的到来

&emsp;&emsp;一般在W3C当中.对版本的发布会经过四个阶段分别是:工作草案（WD）、备选推荐标准（CR）、提案推荐标准（PR）和最终的W3C正式推荐标准（REC）.而当一个规范到达REC阶段时，就意味着它已经得到了W3C成员的正式认可，并推荐它由用户代理部署，前端工程师进行使用。

&emsp;&emsp;现在,就在2017年12月14日W3C发布了[HTML规范5.2更新版本](https://www.w3.org/TR/2017/REC-html52-20171214/index.html),这个版本相对之前的版本来说有了一些增加和删除.对于这些变更,都可以在[HTML 5.2 Changes](https://www.w3.org/TR/html52/changes.html#changes)这个页面上看到.而下面我会对这些变更中某些内容进行介绍.

## 新增的dialog元素

&emsp;&emsp;在HTML5.2中新增了dialog这个标签.来帮助我们快速实现一个对话框.在HTML5.2之前我们想实现一个对话框功能,大概的实现方式的是:一个`div`,先将它`display: none`隐藏掉.然后在需要的地方再讲其`display`置为`black`.这样就能控制一个对话框的显示和隐藏.而在HTML5.2中,你只需要这写:

```JavaScript
<dialog id="dialog">
    <h2>我是对话框标题</h2>
    <p>我是对话框内容</p>
</dialog>
```

就可以得到一个对话框.默认情况下这个对话框是隐藏的,你可以给他添加`open`属性让他显示出来

```JavaScript
<dialog id="dialog" open>
    <h2>我是对话框标题</h2>
    <p>我是对话框内容</p>
</dialog>
```

同时,也可以拿到这个`dialog`元素,然后调用它的`show()`方法让他显示

```JavaScript
<dialog id="dialog" open>
    <h2>我是对话框标题</h2>
    <p>我是对话框内容</p>
</dialog>

<script>

const dialog = document.querySelector('#dialog').show();

</script>
```

同样的,如果那你想要这个对话框隐藏,只需要调用这个`dialog`元素的`close()`方法即可

此外,当你想要在显示对话框的同时,有一个遮罩层的话.你可以使用`showModal()`这个方法.它会在显示对话框的同时,在你页面的顶层显示一个遮罩层,以防止你去操作除对话框以外的它元素.另外要注意一点的是`open`属性和`showModal()`不能同时使用,否则会报;`Untitled-1.html:28 Uncaught DOMException: Failed to execute 'showModal' on 'HTMLDialogElement': The element already has an 'open' attribute, and therefore cannot be opened modally.`这个错误

最后`dialog`的兼容性如下:

![](https://user-gold-cdn.xitu.io/2018/1/9/160db412504765b1?w=2356&h=984&f=png&s=207963)

## 支付请求API --- allowpaymentrequest

在HTML5.2之前.对于支付请求的API是不能在iframe中来完成的.所以每次我们在进行移动支付时都需要跳转到另外一个支付页面才能完成付款.而现在,使用`allowpaymentrequest`属性应用在`iframe`上

```JavaScript
<iframe allowpaymentrequest>
```

这样,就可以让`iframe`使用`Payment Request API`从而让
> 嵌入了第三方内容的页面能够控制该第三方内容是否可向用户请求获取支付凭证，进而让可嵌入的购物车工具可以利用Payment Request API。

> 来自Forrester的Brendan Miller阐述了支付请求API所带来的好处，他说：该新标准让开发者可以创建一个简化的结帐页面，用户可以重复使用保存的付款和地址信息来加快结账速度，并减少错误输入。

Payment Request API的兼容性:

![](https://user-gold-cdn.xitu.io/2018/1/9/160db4167d52b852?w=2352&h=930&f=png&s=196010)

## 在页面上同时使用多个`main`标签

> HTML Main元素`<main>`呈现了文档`<body>`或应用的主体部分。主体部分由与文档直接相关，或者扩展于文档的中心主题、应用的主要功能部分的内容组成。这部分内容在文档中应当是独一无二的，不包含任何在一系列文档中重复的内容，比如侧边栏，导航栏链接，版权信息，网站logo，搜索框（除非搜索框作为文档的主要功能）。

上面是一段是MDN上对于`<main>`标签的介绍.其中说明了`<main>`标签中的内容应该是文档的主题内容,并且是独一无二的.所以,在这段介绍后面有一点注意**在一个文档中 不能 出现一个以上的`<main>`标签**

而现在,随着单页面应用(SPA)的越来越流行.使得这一特性的已经不符合当下的实景,所以在HTML5.2中.在页面中同时出现多个`<main>`标签是被允许的.只要在同一时间内,只有一个`<main>`标签是对外显示的,其他所有都是被`hidden`属性隐藏的即可

```JavaScript
<main>...</main>
<main hidden>...</main>
<main hidden>...</main>
```

此外要注意,隐藏`<main>`标签的方法必须是使用`hidden`属性才行.其它的如:`display: none`、透明的为0等方法不行.这会让`<main>`标签失去自己的语义和作用.

## 被删除的属性和元素

### 被删除的元素

* keygen：用于帮助生成表单的公钥

* menu和menuitem：用于创建导航或上下文菜单

### 被删除的属性

* Window.showModalDialog(): 创建并显示一个包含指定HTML文档的模态框。

* 插件API: 提供有关浏览器插件的信息 .

## 废弃的HTML头部声明

以下两种HTML的头部声明已在HTML中废弃

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
```

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```


上面是我HTML5.2的一些粗浅的总结,希望对大家有所帮助.如果文中有何不当之处请予以斧正,谢谢.

**参考资料**
* [What’s New in HTML 5.2?](https://bitsofco.de/whats-new-in-html-5-2/?utm_source=tuicool&utm_medium=referral)

* [W3C官方推荐使用新发布的HTML5.2](http://www.infoq.com/cn/news/2018/01/html-5-2-recommendation)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
