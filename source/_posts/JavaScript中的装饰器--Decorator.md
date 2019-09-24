---
title: JavaScript中的装饰器--Decorator
date: 2018-3-21 22:43:30
tags:
- JavaScript
- Decorator
- 设计模式
categories:
- 原创
- 前端技术
description: 修饰模式(Decortaor)，是面向对象编程领域中，一种动态地往一个类中添加新的行为的设计模式。就功能而言，修饰模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能。
---

# 什么是Decorator

> &emsp;&emsp;修饰模式(Decortaor)，是面向对象编程领域中，一种动态地往一个类中添加新的行为的设计模式。就功能而言，修饰模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能。

## Decorator的作用

> &emsp;&emsp;通过使用修饰模式，可以在运行时扩充一个类的功能。原理是：增加一个修饰类包裹原来的类，包裹的方式一般是通过在将原来的对象作为修饰类的构造函数的参数。装饰类实现新的功能，但是，在不需要用到新功能的地方，它可以直接调用原来的类中的方法。修饰类必须和原来的类有相同的接口。  
> &emsp;&emsp;修饰模式是类继承的另外一种选择。类继承在编译时候增加行为，而装饰模式是在运行时增加行为。  
> &emsp;&emsp;当有几个相互独立的功能需要扩充时，这个区别就变得很重要。在有些>面向对象的编程语言中，类不能在运行时被创建，通常在设计的时候也不能预测到有哪几种功能组合。这就意味著要为每一种组合创建一个新类。相反，修饰模式是面向运行时候的对象实例的,这样就可以在运行时根据需要进行组合。一个修饰模式的示例是JAVA里的Java I/O Streams的实现。

&emsp;&emsp;上面两段是维基百科中对于`Decorator`装饰器模式的介绍.简单来说.`Decorator`就是一种**动态地往一个类中添加新的行为的设计模式**,它可以在类运行时,扩展一个类的功能.并且去修改类本身的属性和方法.使其可以在不同类之间更灵活的共用一些属性和方法.下面就让我们来看下在ES中`Decorator`的用法.

## Decorator的用法

### 类本身的修饰

&emsp;&emsp;在ES中`Decorator`的具体表现形式为:
> 一个求值结果为函数的表达式，接受目标对象、名称和装饰器描述作为参数，可选地返回一个装饰器描述来安装到目标对象上。

所以说在ES中`Decorator`的最终本质就是一个函数,这个函数通过接受目标对象的三个参数: *所装饰的类的本身*、*所装饰的类的某个属性的key值*、*所装饰的类的某个属性的描述对象*.并通过对这三个参数的操作,已达到为类扩展功能的目的.下面然我们来用具体代码来演示一下:

```javaScript
@eat
class Person {
  constructor() {}
}

function eat(target, key, descriptor) {
  console.log('吃饭');
  console.log(key)
  console.log(descriptor)
  target.prototype.act = '我要吃饭';
}

const jack = new Person();
console.log(jack.act);

// 吃饭
// [Function: Person]
// undefined
// undefined
// 我要吃饭
```

&emsp;&emsp;上面是一个最简单的装饰器的运用.我们首先声明一个类`Person`,然后在声明一个装饰器函数`eat`,在`eat`中将传入的三个参数分别打印出来,并将第一个参数`target`的原型`prototype`上添加一个属性`act`,并赋值为'我要吃饭'.然后将函数`eat`作为装饰在`Person`这个类本身上.最后,构造一个`Person`的实例`jack`,并打印`jack`上的`act`属性.

&emsp;&emsp;然后从下面的运行结果中我们可以看出,代码中会先打印出'吃饭',然后是参数`target`,其次是参数`key`,再然后是参数`descriptor`.最后才是`jack`的`act`属性.这是因为装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。这意味着，装饰器能在编译阶段运行代码。也就是说，装饰器本质就是编译时执行的函数。

### 类的属性的修饰

&emsp;&emsp;看了上面那段代码的运行结果,你可能会用这么一个疑问.`Decorator`所传进来的三个参数: `target`、`key`、`descriptor`.为什么只有`target`有值,而`key`和`descriptor`则都是`undefined`了.事实上这是因为你将装饰器`Decorator`装饰在类本身上所导致的.在ES中装饰器并不仅仅只能装饰在类本身上,也可以装饰在类的属性上.当装饰在类的属性上时.`key`和`descriptor`也就有了用武之地.请看下面这段代码:

```javaScript
class Person {
    constructor() {}

    @test
    name() {console.log('张三');}
}

function test(target, key, descriptor) {
  console.log(target);
  console.log(key);
  console.log(descriptor);
}

const student = new Person();

student.name();

// Person {}
// name
// { value: [Function: name], writable: true, enumerable: false, configurable: true }
// 张三
```

&emsp;&emsp;在上面代码中我们将`test`装饰器装饰在`Person`类的`name`属性上.然后打印三个传入的入参.分别得到了我们期望的结果.而通过这三个参数,我们就可以对我们要装饰的对象进行一些有趣的修改, 如下面这样:

```JavaScript
class Person {

  constructor() {}
    @test
    name() {console.log('张三');}
}

function test(target, key, descriptor) {
  descriptor.value = function () {
    console.log('李四');
  }
}

const student = new Person();

student.name();

// 李四
```

&emsp;&emsp;在上面代码中,我们给一个类`Person`的原型上赋值了一个属性`name`,其值为一个函数,执行时会打印`张三`两个字.然后我们给属性`name`装饰了`test`这个装饰器.在`test`装饰器中,将传入进来的`descriptor`对象上的`value`赋值为一个函数,执行时打印`李四`两个字.最后构造一个实例`student`,并执行`name`方法,执行的结果是打印了`李四`两个字,这说明通过装饰器,我们完全可以在不改变一个类本身的请况下对一个类的属性进行改写.这使得在不同类中共享同一方法这一操作相当简单,且优雅.需要共享的使用使用装饰器,不需要的时候移除装饰器.完全不用对类的本身进行操作.

&emsp;&emsp;对于装饰器,如果我们感到,固定传入的三个参数不够用的话,我们也可以自行传入参数只需要像下面这么写:

```JavaScript

function rename(name) {
  return function(target, key, descriptor) {
    descriptor.value = name;
  }
}

```

前面已经说过,在ES中**`Decorator`就是一个求值结果为函数的表达式,**所以,只要你最后的返回结果是一个函数.都是一个合法的装饰器.

### Decorator的兼容性

&emsp;&emsp;目前ES中`Decorator`还处于提案阶段,各大浏览器和node,均为公开支持这一特性.如果想要使用,则需要借用`babel`的一个插件`babel-plugin-transform-decorators`才可以.

## 结束

&emsp;&emsp;上面是我对`Decorator`的一些见解,希望对大家有所帮助.如果文中任有何不当之处请予以斧正,谢谢

**参考资料:**

* [阮一峰的ES6入门-decorator](http://es6.ruanyifeng.com/#docs/decorator)

* [Decorators in ES7](http://www.liuhaihua.cn/archives/115548.html)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
