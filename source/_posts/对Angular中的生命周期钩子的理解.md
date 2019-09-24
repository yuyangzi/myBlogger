---
title: 对Angular中的生命周期钩子的理解
date: 2017-7-6 20:46:34
tags:
- Angular
- 前端框架
categories:
- 原创
- 前端技术
description: 简单点来说生命周期钩子就是Angular中一个组件从被创建当销毁期间的一些有意义的关键时刻.这些关键时刻在Angular中被Angular核心模块`@angular/core`暴露出来,赋予了我们在它们发生时采取行动的能力.
---

## 什么是生命周期钩子

&emsp;&emsp;简单点来说生命周期钩子就是Angular中一个组件从被创建当销毁期间的一些有意义的关键时刻.这些关键时刻在Angular中被Angular核心模块`@angular/core`暴露出来,赋予了我们在它们发生时采取行动的能力.

## 有哪些生命周期钩子
&emsp;&emsp;Angular中从一个组件的创建到销毁一个有八个生命周期钩子它们,按照先后顺序.它们分别是:
  * `ngOnChanges()`
  * `ngOnInit()`
  * `ngDoCheck()`
  * `ngAfterContentInit()`
  * `ngAfterContentChecked()`
  * `ngAfterViewInit()`
  * `ngAfterViewChecked()`
  * `ngOnDestroy()`

&emsp;&emsp;其中:`ngOnInit()`、`ngAfterContentInit()`、`ngAfterViewInit()`和`ngOnDestroy()`在一个组件的生命周期中只会被调用一次,其它的都有可能会被多次调用.下面,就让我们来详细解列一下这些生命周期钩子.

### ngOnChanges()

> * 当Angular（重新）设置数据绑定输入属性时响应。 该方法接受当前和上一属性值的SimpleChanges对象
> * 当被绑定的输入属性的值发生变化时调用，首次调用一定会发生在ngOnInit()之前。

&emsp;&emsp;`ngOnChanges()`生命周期的调用与一个组件中的输入属性有关.

&emsp;&emsp;当在一个组件中使用`@Input()`定义了一个输入属性时.只要这个输入属性的值发生了改变.就会触发`ngOnChanges()`生命周期钩子.这个生命周期钩子被调用时会传入一个`SimpleChanges`对象,这个对象中包含了输入属性当前值和上一值.

```JavaScript
@Input()
public name: string;

ngOnChanges(changes: SimpleChanges): void {
  console.log(changes); // name:SimpleChange {previousValue: "a", currentValue: "ab", firstChange: false}
}
```

&emsp;&emsp;上面是我定义了一个输入属性`name`并将从`a`它改为`ab`之后的打印结果,可能你还注意到了打印的结果中还有一个`firstChange`属性.它是一个Booleans,表明你是否是第一次改变.

&emsp;&emsp; **同时,还有一点需要注意:** 你的输入属性定义为你引用类型和基本类型的时候其表现结果是不同的.当你的输入属性是基本类型时.你的每一次改变都会触发`ngOnChanges()`生命周期钩子,而当你的输入属性是引用类型时,你改变你引用类型 ***当中*** 的属性时,并不会触发`ngOnChanges()`生命周期钩子.只有当你将你引用类型数据的指针指向另一块内存地址的时候才会触发`ngOnChanges()`生命周期钩子.

### ngOnInit()

> * 在Angular第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件。
> * 在第一轮ngOnChanges()完成之后调用，只调用一次。

&emsp;&emsp;`ngOnInit()`是一个组件的生命周期中一定存在的一个钩子.它在一个组件被初始化的时候被调用.在这个期间,你可以执行一些相应的数据绑定操作.

### ngDoCheck()
> * 检测，并在发生Angular无法或不愿意自己检测的变化时作出反应。
> * 在每个Angular变更检测周期中调用，ngOnChanges()和ngOnInit()之后。

&emsp;&emsp;`ngDoCheck()`是Angular中的变更检测机制.它由`zone.js`来实现的.其行为是只要你的Angular中的某个组件发生异步事件.就会检查整个组件树,以保证组件属性的变化或页面的变化是同步的.所以`ngDoCheck()`的触发相当频繁的.并且是我们无法预料到的.也许我们在页面上的一个无意识操作,就会触发几个甚至几十个的`ngDoCheck()`生命周期钩子.
&emsp;&emsp;所以我们在使用`ngDoCheck()`生命周期钩子的时候一定要加上判断.以避免无用的触发干扰我们.

### ngAfterContentInit()
> * 当把内容投影进组件之后调用。
> * 第一次ngDoCheck()之后调用，只调用一次。
> * 只适用于组件。

&emsp;&emsp;当父组件向子组件投影内容的时.在子组件内会初始化父组件的投影内容,此时会调用`ngAfterContentInit()`生命周期钩子.在整个组件生命周期中`ngAfterContentInit()`生命周期钩子只会调用一次.如下所示:

```
// 父组件
<app-child>
  <p>我是父组件向子组件的投影内容</>
</app-child>


// 子组件 ChildComponent
<div>
  //接受父组件的投影内容
  <ng-content></ng-content>
</div>
```

### ngAfterContentChecked()
> * 每次完成被投影组件内容的变更检测之后调用。
> * ngAfterContentInit()和每次ngDoCheck()之后调用
> * 只适合组件。

&emsp;&emsp;当父组件向子组件的投影内容发生改变时会调用`ngAfterContentChecked()`生命周期钩子.它与`ngDoCheck()`类似.当投影内容发生改变时,就会执行变更检查机制.同时调用`ngAfterContentChecked()`生命周期钩子.此外.还有一点:当父组件和子组件都有投影内容时,会先执行父组件的生命周期钩子.它与下面要说的`ngAfterViewInit()`和`ngAfterViewChecked()`相反.

### ngAfterViewInit()
> * 初始化完组件视图及其子视图之后调用。
> * 第一次ngAfterContentChecked()之后调用，只调用一次。
> * 只适合组件。

&emsp;&emsp;当其组件本身和所有的子组件渲染完成,已经呈现在页面上时,调用`ngAfterViewInit()`生命周期钩子.在整个组件生命周期中`ngAfterViewInit()`生命周期钩子只会调用一次.

### ngAfterViewChecked()
> * 每次做完组件视图和子视图的变更检测之后调用。
> * ngAfterViewInit()和每次ngAfterContentChecked()之后调用。
> * 只适合组件。

&emsp;&emsp;当组件及其子组件的视图发生改变时,执行完变更检查机制后调用.当父组件和子组件都发生视图变化时,会先执行子组件的生命周期钩子.

&emsp;&emsp;**注意:** 这里所说的视图发生改变不一定是真正页面上的变化.只是Angular种所认为的视图变化.因为Angular本身并不能察觉到页面上显示的视图.所以在Angular认为,只要你在后台定义的属性发生了改变,就是视图有了变化.从而就会调用`ngAfterViewChecked()`生命周期钩子.

### ngOnDestroy
> * 当Angular每次销毁指令/组件之前调用并清扫。 在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏。
> * 在Angular销毁指令/组件之前调用。

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
