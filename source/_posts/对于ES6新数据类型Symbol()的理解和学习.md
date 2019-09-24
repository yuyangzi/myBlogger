---
title: 什么是Symbol()类型的数据?
date: 2017-4-15 23:26:34
tags:
- ES6
- JavaScript
- 数据类型
categories:
- 原创
- 前端技术
description: Symbol 是一种特殊的、不可变的数据类型,可以作为对象属性的标识符使用。Symbol 对象是一个 symbol primitive data type 的隐式对象包装器。symbol 数据类型是一个 primitive data type.
---

1. ## 什么是Symbol()类型的数据?
Symbol()是ECMAScript2015(ECMA6)中新推出的一种基本的数据类型.MDN上介绍到:

>  Symbol 是一种特殊的、不可变的数据类型,可以作为对象属性的标识符使用。Symbol 对象是一个 symbol primitive data type 的隐式对象包装器。symbol 数据类型是一个 primitive data type.
---[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

简单点来说:
* ```Symbol```是一种特殊的数据类型,其特点为一旦定义了之后就不可更改,故此适合用来作为对象的属性名.使其在上游定义的属性不会再下游被重写和覆盖掉.
* ```Symbol()```对象是```symbol```基本类型数据的隐式对象包装器.其相当与```String()```和```string```、```Number()```和```number```、```Boole()```和```boole```.
* ```symbol```是基本数据类型.

2. ## Symbol()的基本语法和特性
    * 创建一个Symbol类型的数据:

        1. 创建局部的Symbol:

        ```javascript
        let s = Symbol();

        s // Symbol()
        ```
        2. 创建全局的Symbol:
        ```JavaScript
        let s = Symbol.for();

        s //Symbol(undefined)
        ```

    *注意:* 通过Symbol函数创建Symbol数据时,不能使用```new```关键字,因为```Symbol```是一个基本数据类型,而并不是一个对象.

    * Symbol的实例描述:

    在使用```Symbol()```函数创建Symbol实例时,可以在```Symbol()```函数中传入参数,以用做对Symbol实例的描述.
    ```JavaScript
    let s1 = Symbol("这是一个Symbol实例的描述1");
    let s2 = Symbol("这是一个Symbol实例的描述2");

    s1 // Symbol(这是一个Symbol实例的描述1)
    s2 // Symbol(这是一个Symbol实例的描述2)

    ```
    在上面那段代码中我们创建了两个Symbol类型的实例,如果不添加描述的话,在控制台中打印两个实例.其得到的结果将都是```Symbol```.这样就不太容易区分.而有了描述就可以来区分不同的Symbol实例了.

    * Symbol类型数据的特性:

        1. 每一个Symbol实例都是独一无二的.

        任何情况下两个Symbol实例都不会相等,这个与JavaScript中其他的几种数据类型不同.

        ```JavaScript
        let a = Symbol();
        let b = Symbol();

        a === b // false

        let s1 = Symbol("Symbol实例");
        let s2 = Symbol("Symbol实例");

        s1 //Symbol(Symbol实例)
        s2 //Symbol(Symbol实例)

        s1 === s2 // false
        ```

        2. Symbol实例不能与其他类型的数据进行运算,但可以显示的转换成字符串和布尔值.

        ```JavaScript
        let a = Symbol();
        //和字符串进行运算
        "s" + a //TypeError: Cannot convert a Symbol value to a string

        //和数字进行运算
        1 + a //TypeError: Cannot convert a Symbol value to a number

        //和布尔值进行运算
        false + a //TypeError: Cannot convert a Symbol value to a number

        //和数组进行运算
        [] + a //TypeError: Cannot convert a Symbol value to a string

        //和对象进行运算
        ({}) + a //TypeError: Cannot convert a Symbol value to a string



        //转换成字符串
        a.toString() //"Symbol()"
        String(a) //"Symbol()"

        //转换成布尔值
        !!a //true;

        ```
    * 内置的Symbol值:

        1. **Symbol.hasInstance**

        > 对象的Symbol.hasInstance属性，指向一个内部方法。当其他对象使用instanceof运算符，判断是否为该对象的实例时，会调用这个方法。比如，foo instanceof Foo在语言内部，实际调用的是Foo\[Symbol.hasInstance](foo)。

        2. **Symbol.isConcatSpreadable**

        > 对象的Symbol.isConcatSpreadable属性等于一个布尔值，表示该对象使用Array.prototype.concat()时，是否可以展开。

        3. **Symbol.species**

        > 对象的Symbol.species属性，指向当前对象的构造函数。创造实例时，默认会调用这个方法，即使用这个属性返回的函数当作构造函数，来创造新的实例对象。

        4. **Symbol.match**

        > 对象的Symbol.match属性，指向一个函数。当执行str.match(myObject)时，如果该属性存在，会调用它，返回该方法的返回值。

        5. **Symbol.replace**

        > 对象的Symbol.replace属性，指向一个方法，当该对象被String.prototype.replace方法调用时，会返回该方法的返回值。

        6. **Symbol.search**

        > 对象的Symbol.search属性，指向一个方法，当该对象被String.prototype.search方法调用时，会返回该方法的返回值。

        7. **Symbol.split**

        > 对象的Symbol.split属性，指向一个方法，当该对象被String.prototype.split方法调用时，会返回该方法的返回值。

        8. **Symbol.iterator**

        > 对象的Symbol.iterator属性，指向该对象的默认遍历器方法。

        9. **Symbol.toPrimitive**

        > 对象的Symbol.toPrimitive属性，指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。

        10. **Symbol.toStringTag**

        > 对象的Symbol.toStringTag属性，指向一个方法。在该对象上面调用Object.prototype.toString方法时，如果这个属性存在，它的返回值会出现在toString方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制[object Object]或[object Array]中object后面的那个字符串。

        11. **Symbol.unscopables**

        > 对象的Symbol.unscopables属性，指向一个对象。该对象指定了使用with关键字时，哪些属性会被with环境排除。

3. ## 使用Symbol实例作为对象的属性名

前面说到过,Symbol实例因为其不可变得特性,适合作为对象的属性名来使用.以防止属性被改写和覆盖.然而,Symbol实例怎么样来保证属性名不会被改写了.难道是,用Symbol实例定义的属性就不能修改值了吗?让我们来试试.

```JavaScript
let a = Symbol();
let obj = {};

obj[a] = 10;

obj[a] //10

obj[a] = 20;

obj[a] //20

```

在上面的代码中我们可以看到,使用Symbol实例作为属性名,并不是不能修改的.在最开始我们将```obj[a]```赋值为10,然后打印出10,之后我们再将```obj[a]```修改为20,然后再打印,结果是20.看到这里,我想可能会疑惑,既然使用Symbol实例定义的属性是可以被修改的,那么它的不可变更的特性体现在哪里了.

事实上,Symbol实例的不可变更的特性,是要在两个及两个以上的作用域中才能体现出来.请看下面的代码.

```JavaScript
let a = Symbol();
var obj = {};
{
    obj[a] = 10;
}

{
    let a = Symbol();
    obj[a] = 20;
}


obj //Object {Symbol(): 10, Symbol(): 20}
obj[a] //10

```

在上面代码中可以看到,我们在全局作用域中声明了一个Symbol ```a``` 实例和一个对象 ```obj``` ,然后在一个代码块中将```obj[a] = 10```赋值.之后在另一个代码块中再次声明一个Symbol()的实例 ```a``` ,并将 ```obj[a] = 20```
最后在全局下打印```obj[a]```,结果为10,并没有被改写.而打印```obj```则结果为```Object {Symbol(): 10, Symbol(): 20}```,其定义的两个属性都存在.
这就是Symbol实例的不可变特性,因为每一个Symbol实例都是独一无二的,所以你在另一个代码块中声明同样的一个Symbol实例的变量,也依然不会将之前的给覆盖掉.这就是使用Symbol实例作为属性名的作用.

另外,使用Symbol实例作为属性名还有着一下特性:

1. > Symbol 值作为对象属性名时，不能用点运算符。

2. >  Symbol 作为属性名，该属性不会出现在for...in、for...of循环中，也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。但是，它也不是私有属性，有一个Object.getOwnPropertySymbols方法，可以获取指定对象的所有 Symbol 属性名。


以上就是我对与Symbol这个新数据类型的理解和学习,希望对大家有所帮助.同时如果文中有什么不当之处,请予以斧正,谢谢!


**参考资料** : [ECMAScript 6 入门第十章 Symbol篇](http://es6.ruanyifeng.com/#docs/symbol) 作者：[阮一峰](http://www.ruanyifeng.com/home.html)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
