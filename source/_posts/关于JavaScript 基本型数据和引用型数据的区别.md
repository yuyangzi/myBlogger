---
title: 关于JavaScript 基本型数据和引用型数据的区别。
date: 2017-4-22 17:26:34
tags:
- JavaScript
- 数据类型
categories:
- 原创
- 前端技术
description: 关于JavaScript 基本型数据和引用型数据的区别。
---

JavaScript中有七种数据类型,分别是:
六个基本类型 : Number, String,Null, Boolean, Undefined 以及在ES6新推出的***Symbols***和引用型数据: Object(对象)。

**注意:** Array 、Function、Date....也都是属于Object（对象）。因为它们在原型链上都会有一个内部指针，指向Object.prototype。


### 基本数据类型的特点：
1. 基本数据类型被保存在栈内存中并据有固定的大小：
```JavaScript
var str = "String";
```
&emsp;&emsp;如上声明了一个变量str,并将字符串"String"复制给它.此时在内存中就是下图所示
![基本型数据内存储存结构](http://img.blog.csdn.net/20170410134847480?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
此时在栈内存中变量str所对应的是"String"这个值，而值"String"在内存中所占用的大小是固定的。
也许你会有下面的这个疑问：
```JavaScript
var str = "String";
	str = "abc";
```

这样变量str的大小不就改变了吗？
&emsp;&emsp;是的，这样str的大小的确得到了改变。但是也只是str的大小得到了改变。我们要明确一点：**我们所说的基本数据类型并不是变量str，而是“String”。**变量str只是在“String”被赋值给它之后。指向了"String"这个值。而这个指向在JavaScript中是可以任意更改的。就如在上面的代码中在将变量str声明赋值为"String"之后。又将值“abc”，赋值给了变量str。此时根据JavaScript的规则。对同一变量进行赋值时，后一条语句将覆盖前一条语句。所以此时变量str就将所指向的值改为了"abc"。注意：**这里是将所指向的值改变了，并不是"String"这个基本数据类型的值改变。**

2. 基本数据类型不可以添加属性和方法但可以调用属性和方法。
如下代码所示：
```JavaScript
var str = "String";
str.a = 10;
str.b = function () {
	console.log("hello world");
}

console.log(str.a);// undefined
console.log(str.b);// undefined
```

&emsp;&emsp;在上面代码中首先定义了一个基本类型变量str，然后为其设置属性和方法，最后将其打印出来。而得出的结果都是undefined。由此可以说明**基本数据类型不可以添加属性和方法**。

然后再看下面的代码：

```JavaScript
var str = "String";
var leh = str.length;
var STR = str.toUpperCase();

console.log(leh);//6
console.log(STR);//STRING
```
&emsp;&emsp;在上面代码中一个基本类型变量str调用了toUpperCase()方法和length属性，并成功返回结果。由此可以说明**基本数据类型可以调用属性和方法**。
&emsp;&emsp;而之所以会出现这一现象。是因为基本数据类型本身是不可以调用属性和方法。但是当你尝试去用基本数据类型调用属性和方法时，JavaScript会智能的将其创建一个包装类型对象。并用这个包装类型对象去调用你想要调用的属性和方法。并在调用完成之后立即这个包装类型对象销毁。

3. 基本数据类型的操作是对值的操作。
* 使用基本数据类型的变量对一个新的变量进行赋值。就是将变量的值复制出一个副本，将这个副本赋值给新的变量。所以两个变量之间除了值相等之外没有任何关系。对其中一个变量的操作，并不会影响到另一个变量。
```JavaScript
var str = "String";
var str1 = str；
console.log(str); //String
console.log(str1); //String
str1 = "abc"
console.log(str); //String
console.log(str1); //abc
```
&emsp;&emsp;在上面代码中将变量str赋值给变量str1，此时str与str1的值都是"String"。然后将变量str1的值修改"abc",这时候变量str的值还是为"String"。而变量str1的值为"abc"。


* 两个基本数据类型之间的比较是值与值比较
```JavaScript
var str = "";
var str1 ="";

console.log(str === str1);// true
```
上面代码中两个相同空字符串全等于比较。返回结果为true.

### 引用型数据的特点：
>在 ECMAScript 中，引用类型是一种数据结构，用于将数据和功能组织在一起。它也常被称为类，但这种称呼并不妥当。尽管 ECMAScript从技术上讲是一门面向对象的语言，但它不具备传统的面向对象语言所支持的类和接口等基本结构。引用类型有时候也被称为对象定义，因为它们描述的是一类对象所具有的属性和方法。
>
JavaScript高级程序设计（第3版）


1.引用型数据的指针被保存在栈内存中，其具体的值被保存在堆内存中：
```JavaScript
var obj = new Obiect();
```
&emsp;&emsp;如上声明了一个变量obj,并将字符串"String"复制给它.此时在内存中就是下图所示
![引用型数据内存储存结构](http://img.blog.csdn.net/20170410170033476?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
在JavaScript中创建一个对象并将其赋值给一个变量时。其赋值的并不是这个对象的本身，而是指向这个对象的一个指针。

 2. 

> 当复制保存着对象的某个变量时，操作的是对象的引用。但在为对象添加属性时，操作的是实际的对象 	——图灵社区“壮壮的前端之路”

因为这个特性使得引用型数据有了如下行为的实现：
* 当将一个引用类型的变量赋值给另一个新变量的之后。在新变量上的操作将会影响到旧的变量。
```JavaScript
var obj = new object();
var obj1 = obj;
obj1.a = 10;
console.log(obj.a);//10
```
&emsp;&emsp;在上面的代码中，首先讲一个空对象赋值给变量obj，然后再将变量obj赋值给新变量obj1。在给新变量obj1添加一个属性a并赋值为10，最后打印出变量obj的属性a的值，给出结果10。因为变量obj赋值给变量obj1的是指向空对象的指针，而不是空对象本身。所以此时空对象依然只有一个，但有了两个对它进行操作的入口。

* 两个引用型变量之间的对比，比较的不是具体的值。而是两者指向引用型数据的指针是否一样。

```JavaScript
var obj = {name : "iwen"};
var obj1 = {name : "iwen};
console.log(obj === obj1);//false;
```
&emsp;&emsp;上述代码中定义了两个变量obj 和 obj1 ，并都将其初始化为对象 {name : "iwen"}。然后比较两个值是否相等。结果返回false。这是因为虽然两者的值是一样，但确实两个不同的对象，所以两个变量的内部指针并不相同。故返回false。

3. 引用型数据可以动态的更新大小

```JavaScript
var arr = [1];
console.log(arr.length);//1
arr[1] = 22;
console.log(arr.length);//2

```
&emsp;&emsp;在上面的代码中，首先定义变量arr为引用型数据-Array(数组)。并添加一条数值型数据 --1。此时arr的长度为1，然后再为arr动态添加一条数据--22。此时在打印arr的长度则为2。其大小以自动得到更新。

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
