---
title: 初始RxJS
date: 2017-8-5 23:46:34
tags:
- RxJS
- 观察者模式
- 迭代模式
categories:
- 原创
- 前端技术
description: Rx.JS是英文 Reactive Extensions for JavaScript 的缩写.翻译成中文就是:JavaScript的响应式扩展.其主要的功能就是利用响应式编程的模式来实现JavaScript的异步式编程.
---
# 什么是Rx.JS

&emsp;&emsp;`Rx.JS`是英文 **Reactive Extensions for JavaScript** 的缩写.翻译成中文就是:JavaScript的响应式扩展.其主要的功能就是利用响应式编程的模式来实现JavaScript的异步式编程.  
&emsp;&emsp;相对于JavaScript中其它的异步式解决方式(回调函数、Promise、Gender、async函数而言),Rx.JS有着更加强大的特性和更加优雅的写法.如(纯净性、流动性、值)。

## Rx.JS的基本概念

&emsp;&emsp;Rx.JS的响应式编程操作是结合了观察者模式、迭代模式以及函数式编程来实现的.它主要有着如下几个概念:

> * Observable (可观察对象): 表示一个概念，这个概念是一个可调用的未来值或事件的集合。
> * Observer (观察者): 一个回调函数的集合，它知道如何去监听由 Observable 提供的值。
> * Subscription (订阅): 表示 Observable 的执行，主要用于取消 Observable 的执行。
> * Operators (操作符): 采用函数式编程风格的纯函数 (pure function)，使用像 map、filter、concat、flatMap 等这样的操作符来处理集合。
> * Subject (主体): 相当于 EventEmitter，并且是将值或事件多路推送给多个 Observer 的唯一方式。
> * Schedulers (调度器): 用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 setTimeout 或 requestAnimationFrame 或其他。

&emsp;&emsp;下面,来让我们看一个简单的示例.
&emsp;&emsp;我们都知道,在前端开发当中为性能的优化,我们往往会对某个持续不断会触发的事件做一下事件节流.,也就是事件增加一个`节流阀`,以限制事件的不断触发.最常见的做法就是设定一个定时器.来进行判断.事件在多少秒之内不断触发的话.就将`节流阀`打开.不进行事件操作.直达不断触发事件的行为结束之后,才进行事件操作.最常见的就是输入框的例子.如下面代码所示:

```JavaScript
<input id="text">
<script>
    var text = document.querySelector('#text'),
        timer = null,
        currentSearch = '';

    text.addEventListener('keyup', (e) =>{
        clearTimeout(timer)
        timer = setTimeout(() => {
            // 声明一个当前所搜的状态变量
            currentSearch ＝ '书';

            var searchText = e.target.value;
            $.ajax({
                url: `search.qq.com/${searchText}`,
                success: data => {
                    // 判断后台返回的标志与我们存的当前搜索变量是否一致
                    if (data.search === currentSearch) {
                        // 渲染展示
                        render(data);
                    } else {
                        // ..
                    }
                }
            });
        },250)
    })
</script>
```

&emsp;&emsp;上面的代码就是一个很常见的一个事件节流的用法.当你按下键盘触发事件时,并不是直接发送AJAX请求.而是使用`setTimeout`延迟250毫秒.在延迟的过程中如果有新的事件触发.就会重新计算延迟.如此不断的反复.直到两次触发事件的间隔大于250毫秒.才会真正的发送AJAX请求.

&emsp;&emsp;下面让我们来看看上述的逻辑用RxJS怎样的实现.

```JavaScript
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
  .debounceTime(250)
  .subscribe(function(){
      $.ajax({
        url: `search.qq.com/${searchText}`,
        success: data => {
          // 判断后台返回的标志与我们存的当前搜索变量是否一致
          if (data.search === currentSearch) {
            // 渲染展示
            render(data);
          } else {
            // ..
          }
        }
    });
  });
```

&emsp;&emsp;上面是用RxJS实现的同样的功能.但我们看到其代码精简了许多.上述代码中首先获取到一个`button`元素.然后使用`Rx.Observable.fromEvent`创建了一个被观察对象.其对象所监听的是`button`元素的`.click`事件.每当`button`元素的`click`事件触发时.就会发射一个值出去.这个值途中经过`debounceTime`操作符,延时了250毫秒.然后再被观察者对象捕获.最终触发其定义的AJAX事件.这就是上述代码中的实现过程.  
&emsp;&emsp;然而我们看到.虽然上述在定义执行的过程中做了许多的事情,但这些是RxJS在内部帮助我们就做好了的.我们只需要它给我们的API方法就可以实现用几段简单的代码,实现一段复杂的操作.


### Observable (可观察对象)

> Observables 是多个值的惰性推送集合

&emsp;&emsp;Observable是RxJS的核心概念之一.它实际上就是可以被外界观察的一个对象.当本身的状态发生变化时,就会将其变化推送给外界观察它的对象,也就是 **观察者对象**.同时因为`Observables 是多个值的惰性推送集合`所以只有当使用一个观察者对象去订阅了它之后.它才会同步或异步地返回零到(有可能的)无限多个值.下面是使用RxJS创建一个`Observable`的方式

```JavaScript
var observable = Rx.Observable.create(function subscribe(observer) {
  var id = setInterval(() => {
    observer.next('hi')
  }, 1000);
});
```

上面实例创建了一个 Observable，它每隔一秒会向观察者发送字符串 'hi'.

### Observer (观察者)

> 什么是观察者？ - 观察者是由 Observable 发送的值的消费者。观察者只是一组回调函数的集合，每个回调函数对应一种 Observable 发送的通知类型：next、error 和 complete 。

&emsp;&emsp;简单来说,`Observer`就是使用`Observable`发送出来值的一个方法集合.当一个`Observable`发送出来值之后由`Observer`来决定如何的去使用它.而使用的方式就是通过回调函数.将`Observable`发送出来的值作为参数传入其中.让后在内部去使用.同时根据`Observable`发送出来的值不同.其调用的回调函数也不同.分别有`next`(下一步),`error`(报错),`complete`(结束).下面是使用`Observer`的方法:

```JavaScript
observable.subscribe(observer);
```

> 要使用观察者，需要把它提供给 Observable 的 subscribe 方法

### Subscription (订阅)

> 什么是 Subscription ？ - Subscription 是表示可清理资源的对象，通常是 Observable 的执行。Subscription 有一个重要的方法，即 unsubscribe，它不需要任何参数，只是用来清理由 Subscription 占用的资源。在上一个版本的 RxJS 中，Subscription 叫做 "Disposable" (可清理对象)。

&emsp;&emsp;`Subscription`(订阅)是使用`observable.subscribe()`创建一个观察者对象时.所返回的一个对象.它主要就是使用` unsubscribe() 函数`主动关闭`Observer`对`Observable`的监听订阅.其使用方法如下:

```JavaScript
var observable = Rx.Observable.interval(1000);
var subscription = observable.subscribe(x => console.log(x));
// 稍后：
// 这会取消正在进行中的 Observable 执行
// Observable 执行是通过使用观察者调用 subscribe 方法启动的
subscription.unsubscribe();
```

### Operators (操作符)

> 操作符是 Observable 类型上的方法，比如 .map(...)、.filter(...)、.merge(...)，等等。当操作符被调用时，它们不会改变已经存在的 Observable 实例。相反，它们返回一个新的 Observable ，它的 subscription 逻辑基于第一个 Observable 。
> 操作符是函数，它基于当前的 Observable 创建一个新的 Observable。这是一个无副作用的操作：前面的 Observable 保持不变。

&emsp;&emsp;就本质上而言`Operators`就是一个纯粹的函数.它可以接收一个 Observable 作为输入.并在经过内部的一系列处理后返回一个新的`Observable`作为输出.流向下一个操作.

### Subject (主体)

> 什么是 Subject？ - RxJS Subject 是一种特殊类型的 Observable，它允许将值多播给多个观察者，所以 Subject 是多播的，而普通的 Observables 是单播的(每个已订阅的观察者都拥有 Observable 的独立执行)。
> Subject 像是 Observalbe，但是可以多播给多个观察者。Subject 还像是 EventEmitters，维护着多个监听器的注册表。

&emsp;&emsp;每一个`Subject`都同时是一个`Observable`和`Observer`.对于`Subject`你可以使用`subscribe`方法并指定一个观察者.也可以调用`next(v)`、`error(e)` 和 `complete()`来处理接受道到值.示例如下:

```JavaScript

var subject = new Rx.Subject();

subject.subscribe({
  next: (v) => console.log('observerA: ' + v)
});
subject.subscribe({
  next: (v) => console.log('observerB: ' + v)
});

subject.next(1);
subject.next(2);
```

在上面的示例中，我们为 Subject 添加了两个观察者，然后给 Subject 提供一些值

### Schedulers (调度器)

> 什么是调度器？ - 调度器控制着何时启动 subscription 和何时发送通知。它由三部分组成：
> * 调度器是一种数据结构。 它知道如何根据优先级或其他标准来存储任务和将任务进行排序。
> * 调度器是执行上下文。 它表示在何时何地执行任务(举例来说，立即的，或另一种回调函数机制(比如 setTimeout 或 process.nextTick)，或动画帧)。
> * 调度器有一个(虚拟的)时钟。 调度器功能通过它的 getter 方法 now() 提供了“时间”的概念。在具体调度器上安排的任务将严格遵循该时钟所表示的时间。
> 调度器可以让你规定 Observable 在什么样的执行上下文中发送通知给它的观察者。

上面是我最近学习RxJS时一些粗浅的总结,希望对大家有所帮助.如果文中有何不当之处请予以斧正,谢谢.
**参考资料**

* [RxJS中文手册](http://cn.rx.js.org/manual/overview.html#h11)

* [构建流式应用：RxJS 详解](https://www.qcloud.com/community/article/191068?fromSource=gwzcw.107650.107650.107650)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
