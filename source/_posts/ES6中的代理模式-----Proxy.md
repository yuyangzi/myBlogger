---
title: ES6中的代理模式-----Proxy
date: 2018-1-7 22:30:34
tags:
- ES6
- Proxy
- 代理模式
categories:
- 原创
- 前端技术
description: 简单来说:Proxy对象就是可以让你去对JavaScript中的一切合法对象的基本操作进行自定义.然后用你自定义的操作去覆盖其对象的基本操作.也就是当一个对象去执行一个基本操作时,其执行的过程和结果是你自定义的,而不是对象的.
---

# 什么是代理模式

> 代理模式（英语：Proxy Pattern）是程序设计中的一种设计模式。

> 所谓的代理者是指一个类别可以作为其它东西的接口。代理者可以作任何东西的接口：网络连接、内存中的大对象、文件或其它昂贵或无法复制的资源。

> 著名的代理模式例子为引用计数（英语：reference counting）指针对象。

> 当一个复杂对象的多份副本须存在时，代理模式可以结合享元模式以减少内存用量。典型作法是创建一个复杂对象及多个代理者，每个代理者会引用到原本的复杂对象。而作用在代理者的运算会转送到原本对象。一旦所有的代理者都不存在时，复杂对象会被移除。

上面是维基百科中对代理模式的一个整体的定义.而在JavaScript中代理模式的具体表现形式就是ES6中的新增对象---**[Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)**

## 什么是Proxy对象

在MDN上对于`Proxy`的解释是:
> Proxy 对象用于定义基本操作的自定义行为（如属性查找，赋值，枚举，函数调用等）。

&emsp;&emsp;简单来说:`Proxy`对象就是可以让你去对JavaScript中的一切合法对象的基本操作进行自定义.然后用你自定义的操作去覆盖其对象的基本操作.也就是当一个对象去执行一个基本操作时,其执行的过程和结果是你自定义的,而不是对象的.

😓好吧,用文字表达可能太复杂了.我们还是直接上代码吧.

首先Proxy的语法是:

 ```JavaScript
 let p = new Proxy(target, handler);
 ```
其中:

* `target`是你要代理的对象.它可以是JavaScript中的任何合法对象.如: (数组, 对象, 函数等等)

* `handler`是你要自定义操作方法的一个集合.

* `p`是一个被代理后的新对象,它拥有`target`的一切属性和方法.只不过其行为和结果是在`handler`中自定义的.

然后让我们来看这段代码:

```JavaScript
let obj = {
  a: 1,
  b: 2,
}

const p = new Proxy(obj, {
  get(target, key, value) {
    if (key === 'c') {
      return '我是自定义的一个结果';
    } else {
      return target[key];
    }
  },

  set(target, key, value) {
    if (value === 4) {
      target[key] = '我是自定义的一个结果';
    } else {
      target[key] = value;
    }
  }
})

console.log(obj.a) // 1
console.log(obj.c) // undefined
console.log(p.a) // 1
console.log(p.c) // 我是自定义的一个结果

obj.name = '李白';
console.log(obj.name); // 李白
obj.age = 4;
console.log(obj.age); // 4

p.name = '李白';
console.log(p.name); // 李白
p.age = 4;
console.log(p.age); // 我是自定义的一个结果
```

从上面这段代码中,我可以很清楚的看到`Proxy`对象的作用.即是之前所受的**用于定义基本操作的自定义行为**.同样的`get`和`set`操作.没有没代理的对象所得的结果是其JavaScript本身的执行机制运行计算后所得到的.而被代理了的对象的结果则是我们自定义的.

### Proxy所能代理的范围--handler

在上面代码中,我们看到了构造一个代理对象时所传的第二个参数`handler`,这个`handler`对象是由`get`和`set`两个函数方法组成的.这两个方法会在一个对象被`get`和`set`时被调用执行,以代替原生对象上的操作.那么为什么在`handler`,定义`get`和`set`这两个函数名之后就代理对象上的`get`和`set`操作了呢?

实际上`handler`本身就是ES6所新设计的一个对象.它的作用就是用来**自定义代理对象的各种可代理操作**。它本身一共有13中方法,每种方法都可以代理一种操作.其13种方法如下:

```JavaScript
handler.getPrototypeOf()

// 在读取代理对象的原型时触发该操作，比如在执行 Object.getPrototypeOf(proxy) 时。

handler.setPrototypeOf()

// 在设置代理对象的原型时触发该操作，比如在执行 Object.setPrototypeOf(proxy, null) 时。

handler.isExtensible()

// 在判断一个代理对象是否是可扩展时触发该操作，比如在执行 Object.isExtensible(proxy) 时。

handler.preventExtensions()

// 在让一个代理对象不可扩展时触发该操作，比如在执行 Object.preventExtensions(proxy) 时。

handler.getOwnPropertyDescriptor()

// 在获取代理对象某个属性的属性描述时触发该操作，比如在执行 Object.getOwnPropertyDescriptor(proxy, "foo") 时。

handler.defineProperty()

// 在定义代理对象某个属性时的属性描述时触发该操作，比如在执行 Object.defineProperty(proxy, "foo", {}) 时。

handler.has()

// 在判断代理对象是否拥有某个属性时触发该操作，比如在执行 "foo" in proxy 时。

handler.get()

// 在读取代理对象的某个属性时触发该操作，比如在执行 proxy.foo 时。

handler.set()

// 在给代理对象的某个属性赋值时触发该操作，比如在执行 proxy.foo = 1 时。

handler.deleteProperty()

// 在删除代理对象的某个属性时触发该操作，比如在执行 delete proxy.foo 时。

handler.ownKeys()

// 在获取代理对象的所有属性键时触发该操作，比如在执行 Object.getOwnPropertyNames(proxy) 时。

handler.apply()

// 在调用一个目标对象为函数的代理对象时触发该操作，比如在执行 proxy() 时。

handler.construct()

// 在给一个目标对象为构造函数的代理对象构造实例时触发该操作，比如在执行new proxy() 时。
```

### Proxy的作用

对于代理模式`Proxy`的作用主要体现在三个方面:

1、 拦截和监视外部对对象的访问

2、 降低函数或类的复杂度

2、 在复杂操作前对操作进行校验或对所需资源进行管理

而对于这三个使用方面的具体表现大家可以参考这篇文章--[实例解析ES6 Proxy使用场景](https://www.w3cplus.com/javascript/use-cases-for-es6-proxies.html)

### Proxy的兼容性

![](https://user-gold-cdn.xitu.io/2018/1/7/160d0f48c3e56c42?w=2350&h=986&f=png&s=192887)

上面是我的一些粗浅的总结,希望对大家有所帮助.如果文中有何不当之处请予以斧正,谢谢.

**参考资料:**

* [MDN---Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

* [实例解析ES6 Proxy使用场景](https://www.w3cplus.com/javascript/use-cases-for-es6-proxies.html)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
