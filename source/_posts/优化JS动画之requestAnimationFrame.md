---
title: 优化动画之requestAnimationFrame
date: 2017-4-04 22:46:14
tags:
- CSS
- 页面动画
categories:
- 原创
- 前端技术
description: window.requestAnimationFrame() 方法告诉浏览器您希望执行动画，并请求浏览器调用指定的函数在下一次重绘之前更新动画。该方法将在重绘之前调用的回调作为参数。
---

## 什么是requestAnimationFrame

&emsp;&emsp;```requestAnimationFrame```(请求动画帧)是浏览器的全局对象window下的一个方法,其作用是用来实现页面中的某些动画效果.在[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)上的描述是:

> window.requestAnimationFrame() 方法告诉浏览器您希望执行动画，并请求浏览器调用指定的函数在下一次重绘之前更新动画。该方法将在重绘之前调用的回调作为参数。

## 为什么要使用requestAnimationFrame

&emsp;&emsp;或许有人会疑问,用JS执行页面动画效果不是有```setInterval```和```setTimeout```了吗,那为什么还要使用```requestAnimationFrame```,
它有着什么样的好处.

&emsp;&emsp;很多人都知道,由于JS的单线程特性以及使用事件队列的运行机制使得在使用```setinterval```和```setTimeout```方法来实现的JS动画其效果往往差强人意.

&emsp;&emsp;这是因为单线程的JS在每一个时间段内只能执行一个任务,如遇到多个任务同时触发,也会先只执行一个.将剩余的放入事件队列当中.等到当前任务执行完了之后,在从队列中取出下一个任务进行执行,并在执行完成之后,再取出下一个任务.如此往复,直至事件队列的任务都执行完毕.这样就导致了JS在事件队列的任务执行完之前,会造成阻塞,在这期间JS是不会区执行其他的任务请求,包括```setinterval```和```setTimeout```这两个定时器的触发.由此就导致了```setinterval```和```setTimeout```的触发执行时间无法把控,从而造成页面上的JS动画效果不理想.

请看下面代码:

```javascript
setTimeout(function(){
  console.log(1);
},0)

console.log(2);

// 2
// 1

```

&emsp;&emsp;在上面这段代码中我们首先设定了一个定时器,并指定它在0毫秒之后触发,```console.log```打印出1,之后又在定时器的下面写了一个```console.log```打印出2.就上面的代码来看,不考虑其它的执行因素,它的结果应该是先打印出1,在打印出2.因为0毫秒就是相当于立即触发,不会等待.而事实上,在JS的运行环境中的结果是先打印出2,再打印出1.所以这段代码的实际运行过程是:  
&emsp;&emsp;*先定义一个定时器,但不去管它的触发时间.因为当前的任务要求只是要设定一个定时器.然后执行下一个任务.```console.log```打印2.之后将当前队列中的任务执行完毕后,再去检查定时是否到达了指定的触发时间.如果达到则执行定时器中的代码由此```console.log```打印出1*

&emsp;&emsp;以上就是一个定时器的大概执行过程,在这个过程中JS永远只会在当前的事件队列里的人物执行完毕后才会去检查定时是否到达指定的触发时间.哪怕在这期间的耗时超过了定时器的指定触发时间,也不会例外.所以这就是为什么很多使用```setinterval```和```setTimeout```做的JS动画效果越往后会感觉越卡的原因所在了.而由此针对这个问题就诞生了```requestAnimationFrame```这个专门的JS动画API.

## requestAnimationFrame的优势

> &emsp;&emsp;requestAnimationFrame的优势，在于充分利用显示器的刷新机制，比较节省系统资源。显示器有固定的刷新频率（60Hz或75Hz），也就是说，每秒最多只能重绘60次或75次，requestAnimationFrame的基本思想就是与这个刷新频率保持同步，利用这个刷新频率进行页面重绘。此外，使用这个API，一旦页面不处于浏览器的当前标签，就会自动停止刷新。这就节省了CPU、GPU和电力。  
&emsp;&emsp;不过有一点需要注意，requestAnimationFrame是在主线程上完成。这意味着，如果主线程非常繁忙，requestAnimationFrame的动画效果会大打折扣。

## requestAnimationFrame的语法

&esmp;&esmp;requestAnimationFrame的语法的语法很简单:

```JavaScript
requestID = window.requestAnimationFrame(callback);
```

只需要window对象调用```requestAnimationFrame```方法.并传入一个回调函数即可.跟```setinterval```和```setTimeout```不同它无需指定动画的间隔时间.因为它的间隔时间就是显示器的刷新频率.同时,调用了```requestAnimationFrame```后将会返回一个长整型非零值,作为一个唯一的标识符.你可以将该值作为参数传给```window.cancelAnimationFrame()```来取消这个回调函数.

下面是由[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)上给出的一个例子

```JavaScript

window.requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;

var start = null;
var d = document.getElementById('SomeElementYouWantToAnimate');
function step(timestamp) { 
  if (start === null) start = timestamp;
  var progress = timestamp - start;
  d.style.left = Math.min(progress/10, 200) + "px";
  if (progress < 2000) {
    requestAnimationFrame(step);
  }
}
requestAnimationFrame(step);

```

## requestAnimationFrame的兼容性

![requestAnimationFrame的兼容性](http://img.blog.csdn.net/20170705221735247?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**参考资料 :**

1. [MDN - window.requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)

2. [JavaScript 标准参考教材 - requestAnimationFrame](http://javascript.ruanyifeng.com/htmlapi/requestanimationframe.html)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
