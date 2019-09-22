---
title: 对ES6Generator函数的理解
date: 2017/5/28 22:21:34
tags:
- ES6
- JavaScript
- 异步编程
categories:
- 原创
- 前端技术
description: Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。
---

## 什么是Generator函数

> Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

&emsp;&emsp;简单说Generator函数是ES6新推出的一种语法,其目的是为了给JS的异步式编程提供一种更好的解决方案.在传统的回调函数异步式编程中,存在的一个最大的问题就是,如果要进行大量的异步式操作时,那么其代码将会一个嵌套者一个.这样一旦嵌套过多,将会造成代码难以理解.并且其代码之间高度耦合.

```JavaScript
$.get("./data1.json",function(data1){
  console.log(data1)
  $.get("./data2.json",function(data2){
    console.log(data2)
    $.get("./data3.json",function(data3){
      console.log(data3);
    })
  })
})
```

## Generator函数的基本概念

> &emsp;&emsp;Generator 函数有多种理解角度。从语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。  
&emsp;&emsp;执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。  
&emsp;&emsp;形式上，Generator 函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；二是，函数体内部使用yield表达式，定义不同的内部状态（yield在英语里的意思就是“产出”）。
Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

上面是[阮一峰](http://www.ruanyifeng.com/home.html)所写的[ECMAScript 6 入门](http://es6.ruanyifeng.com/)中对于Generator函数概念的描述.

&emsp;&emsp;对此我的理解是Generator函数在形式上没有什么区别.只是在定义的时候需要在```function```关键字后面加上 ```*```符号.其次,在执行的时候,Generator函数内部如果没有```yield```关键字时,那就一个有着依次暂停的普通函数.

```JavaScript
function * fn(str) {
  console.log(str);
}

let f = fn("aaaa");

console.log(f) //{[[GeneratorStatus]]: "suspended"}

f.next(); // aaaa
```
&emsp;&emsp;从是上代码中可以看到,代码中首先定义了一个Generator函数```fn```,```fn```函数可以打印一个传进来的字符串.然后调用这个函数```fn```
并将函数的返回值传给变量```f```.再打印```f```.最后用```f```调用```nex()```方法.   
&emsp;&emsp;而在执行了上述代码后可以看到.函数在第一次调用时并没有执行内部的代码,而是返回了一个对象,在使用这个对象调用了```next```方法之后才真正的执行了函数内部的代码.这就是普通的Generator函数在内部没有```yield```关键字时的执行过程.除了有一次暂停外,就结果而言,它与普通函数并没有什么不同.
&emsp;&emsp;

&emsp;&emsp;但是当```Generator```函数中使用了```yield```关键字后.代码的执行就有了改变.

```JavaScript
function * fn() {
  console.log("a");
  let a = "abc";
  yield a;
  
  console.log("b");
  let b = "123";
  yield b;

  let c = "张三"
  return c;
}


let f = fn();

console.log(f.next()) // a
// {value: "abc", done: false}
console.log(f.next()) // b
// {value: "123", done: false}
console.log(f.next())
// {value: undefined, done: true}
console.log(f.next())
// {value: undefined, done: true}

```

&emsp;&emsp;在上面的代码中,我们定义了一个```Generator```函数,并在函数的内部定义了两个```yield```关键字,然后执行这个函数,并在相应的地方打印出值.在打印的结果中可以看出.```yield```关键字在函数运行中会将函数暂停.这个时候只有再次调用```next()```方法才能继续执行```Generator```函数.同时```yield```还将会返回一个对象,这个对象中包含两个属性.一个是```value```,一个是```done```.其中```value```值是对应的```yield```关键字后面所跟的值.而```done```则表示所属的```Generator```函数后面是否还有需要执行的代码.

&emsp;&emsp;上面是我个人对```Generator```函数的运行机制的一个理解,说的可能有点乱.没有表述清楚.所以我在呢下面引用了[阮一峰](http://www.ruanyifeng.com/home.html)在[ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/generator)中对```Generator```函数的描述.希望对大家有所帮助

> 总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；done属性是一个布尔值，表示是否遍历结束。

> &emsp;&emsp;由于 Generator 函数返回的遍历器对象，只有调用next方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。yield表达式就是暂停标志。  
遍历器对象的next方法的运行逻辑如下。  
（1）遇到yield表达式，就暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，作为返回的对象的value属性值。  
（2）下一次调用next方法时，再继续往下执行，直到遇到下一个yield表达式。  
（3）如果没有再遇到新的yield表达式，就一直运行到函数结束，直到return语句为止，并将return语句后面的表达式的值，作为返回的对象的value属性值。  
（4）如果该函数没有return语句，则返回的对象的value属性值为undefined。  
&emsp;&emsp;需要注意的是，yield表达式后面的表达式，只有当调用next方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

## 浏览器兼容性

PC端:

![这里写图片描述](http://img.blog.csdn.net/20170718014018005?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

移动端:
![这里写图片描述](http://img.blog.csdn.net/20170718014308066?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**参考文档:**[阮一峰](http://www.ruanyifeng.com/home.html) - [ECMAScript 6 入门](http://es6.ruanyifeng.com/)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
