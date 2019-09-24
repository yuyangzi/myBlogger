---
title: CSS 新特性之Native Variables、Feature Queries、Grid Layout
date: 2017-4-16 12:46:14
tags:
- CSS
- 页面布局
categories:
- 原创
- 前端技术
description: 前端的发展真的是日新月异,在2017年,虽然CSS4依然在草案当中尚未发布,但是,CSS依然新增了许多新的特性.其中,Native Variables(本地变量)、Feature Queries（特征查询）、Grid Layout（栅格布局）这三个特性是最然我感到激动,因为使用它们,可以很大程度上增强你的开发效率和用户体验.下面就让我来一一介绍他们吧.
---

&emsp;&emsp;前端的发展真的是日新月异,在2017年,虽然CSS4依然在草案当中尚未发布,但是,CSS依然新增了许多新的特性.其中,Native Variables(本地变量)、Feature Queries（特征查询）、Grid Layout（栅格布局）这三个特性是最然我感到激动,因为使用它们,可以很大程度上增强你的开发效率和用户体验.下面就让我来一一介绍他们吧.

## Native Variables(本地变量)

***CSS Native Variables 是由CSS开发人员在编写CSS时按一定规则所自定义的一个实体字符,其作用为包含在整个CSS文件当中所重复使用的特定值.***

> ### 解决的问题
> &emsp;&emsp;在构建大型站点时，作者通常会面对可维护性的挑战。在这些网页中， 所使用的 CSS 的数量是非常庞大的，并且在许多场合大量的信息会重复使用。例如，在网页中维护一个配色方案，意味着一些颜色在CSS文件中多次出现，并被重复使用。当你修改配色方案时，不论是调整某个颜色或完全修改整个配色，都会成为一个复杂的问题，不容出错，而单纯查找替换是远远不够的。    
> &emsp;&emsp;如果使用了 CSS 框架，这种情况会变得尤其糟糕，此时如果要修改颜色，则需要对框架本身进行修改。在这些场合使用 LESS 或 Sass 类似的预处理器是非常有帮助的，但是这种通过添加额外步骤的方式，可能会增加系统的复杂性。CSS变量为我们带来一些预处理器的便利，并且不需要额外的编译。    
> &emsp;&emsp;这些变量的第二个优势就是名称本身就包含了语义的信息。CSS 文件变得易读和理解。main-text-color比文档中的#00ff00更容易理解，特别是同样的颜色出现在不同的文件中的时候。

> ### CSS变量能帮助我们干什么
> &emsp;&emsp;在一些命令式编程语言中，像Java、C++亦或是JavaScript，通过变量我们能够跟踪某些状态。变量是一种符号，关联着一个特定的值，变量的值能随着时间的推移而改变。    
> &emsp;&emsp;在像CSS这种声明式语言中，随着时间而改变的值并不存在，也就没有所谓变量的概念了。    
> &emsp;&emsp;CSS 引入了一种层级变量的概念，从而能够从容应对可维护性的挑战。这就会使得在整个 CSS tree 中都可以象征性的引用一个变量。


1. 基本语法:

    * 声明一个变量:

    ```css
    element {
    --main-bg-color: brown;
    }
    ```
    &emsp;&emsp;上一个选择器的声明中,使用```--```作为前缀,并在后面跟上你自定义的实体字符,即可定义一个变量.之后,使用冒号":"来声明在当前CSS文件中需要重复使用的一个特定值.

    &emsp;&emsp;事实上,这种变量的声明方与你直接使用```width```、```color```这些常规的属性并没有什么不同,只是需要加上```--```的前缀,并且其属性名是自定义的.所以 ***CSS变量也被称为CSS自定义属性***.

    ***注意: 在之前的标准中，自定义属性以```var-```作为前缀，后来才改成``--``前缀.***


    * 使用一个变量

    ```css
    element {
    background-color: var(--main-bg-color);
    }
    ```

    &emsp;&emsp;在声明了一个变量之后,想要使用它,只需要用```var()```括号包裹着你所自定义的实体符号即可.

    ***注意: 在之前的标准中，自定义属性以```var-```作为前缀，后来才改成``--``前缀.***

    * 变量的特性

        1. 变量值只能用作属性值，不能用作属性名

        2. 如果变量值是一个字符串，可以与其他字符串拼接。
        ```css
        element {
            --a : "hello";
            --b : var(--a)" world";
        }

        element::after {
            /* 等同 content: hello world */
            content: var(--b);
            display:block;
            width:100%;
            height:100%;
        }
        ```

        3. 如果变量值是数值,不能与数值单位直接连用,必须使用calc()函数,将它们连接
        ```CSS
        element {
            --a : 200;
            width: var(--a)px;/*无效*/
            width: calc(var(--a) * 1px);/*有效*/
        }
        ```

        4. 如果变量值带有单位，就不能写成字符串。

        ```CSS
        element {
            /*无效写法*/
            --a : "20px";
            font-size : var(--a);
        }

        element {
            /*正确写法*/
            --a : 20px;
            font-size : var(--a);
        }
        ```

        5. 同一个 CSS 变量,可以在多个选择器内声明.读取的时候,按照 CSS 的"层叠"（cascade）规则,会读取优先级最高的声明。
        ```CSS
        <style>
            :root { --color: blue; }
            div { --color: green; }
            #alert { --color: red; }
            * { color: var(--color); }
        </style>
        ```
        ```HTML
        <p>蓝色</p>
        <div>绿色</div>
        <div id="alert">红色</div>
        ```

2. 浏览器兼容性:
![这里写图片描述](http://img.blog.csdn.net/20170618015948328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## Feature Queries（特征查询）

&emsp;&emsp;多年以来,许多前端开发人员都是使用[Modernizr](https://modernizr.com/)这个js脚本库来实现浏览器的特征查询,让我们知道浏览器浏览器所提供的功能,以此针对性的实现某些功能或者特性.使高级浏览器有着更酷炫的效果,也使低级浏览器可以完成基本的功能.从而来提升用户体验.

&emsp;&emsp;但是Modernizr并不是完美的无懈可击的,他也有着自己的缺陷.其中最重要的一条就是执行效率的问题.Modernizr需要JavaScript.虽然其脚本可能很小,但是使用Modernizr构建的CSS需要在应用CSS之前下载,执行和完成JavaScript文件.而涉及JavaScript操作总是要比仅使用CSS慢的.

&emsp;&emsp;所以,由此.Feature Queries（特征查询）就应运而生.因为他是被CSS原生支持的.所以使用起来,更快、更强大、更简单.

### 什么是Feature Queries?

&emsp;&emsp;Feature Queries(特征查询)是一种由CSS原生支持提供的一种浏览器特征检测方法.其查询会分析浏览器是否正确地实现了个你所查询的CSS属性,并由此来决定是否应用一个CSS代码块.

### 语法
```CSS
@supports (supports_condition) {
  /* specific rules */
}
```

使用@supports规则,在后面跟上你要查询的表达式.当表达式的结果返回true时,执行代码块中的代码.此外在要查询的表达式可以使用操作符,以此来实现更复杂的逻辑.同时,也可以使用小括号```()```来提升操作符的优先级.

* not操作符
> not操作符可以放在任何表达式的前面来产生一个新的表达式,新的表达式为原表达式的值的否定.如果transform-origin的实现语法不认为10em 10em 10em是有效的.则下面的表达式会返回true.

```CSS
not( transform-origin: 10em 10em 10em )
```

***注意*** : 如果not操作符位于表达式的最外层,则没有必要使用小括号将它括起来.但如果要将该表达式与其他表达式连接起来使用,比如and和or,则需要外面的小括号.

* and操作符

> and操作符用来将两个原始的表达式做逻辑与后生成一个新的表达式, 如果两个原始表达式的值都为真,则生成的表达式也为真.在下例中,当且仅当两个原始表达式同时为真时,整个表达式才为真:

```css
(display: table-cell) and (display: list-item)
```

* or操作符

> or操作符用来将两个原始的表达式做逻辑或后生成一个新的表达式, 如果两个原始表达式的值有一个为真或者都为真,则生成的表达式也为真.在下例中,当两个原始表达式中至少有一个为真时,整个表达式才为真:

```CSS
( transform-style: preserve ) or ( -moz-transform-style: preserve )
```

***注意***: 在使用and和or操作符时,如果是为了定义多个表达式的执行顺序,则必须使用小括号.如果不这样做的话,该条件就是无效的,会导致整个表达式规则失效.

### 浏览器兼容性:
![这里写图片描述](http://img.blog.csdn.net/20170618020032948?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)




## Grid Layout（栅格布局）

&emsp;&emsp;如果你了解Bootstrap这个经典的前端响应式框架.那么就会知道其中的栅格布局.在Bootstrap中可以将页面最多分割为12列的栅格.以此来实现在不同尺寸设备中的响应式.

### 什么是栅格布局:

> &emsp;&emsp;在一个有限的、固定的平面上，用水平线和垂直线（虚拟的线，“参考线”），将平面划分成有规律的一系列“格子”（虚拟的格子），并依托这些格子、或以格子的边线为基准线，来进行有规律的版面布局。  
&emsp;&emsp;栅格化设计 就是把网页的宽度分成固定的相同宽度，然后列出各种组合的可能，以方便在进行页面呈现的时候能够快速的进行布局，通常分成12等分或者24等分  
&emsp;&emsp;以规则的网格阵列来指导和规范网页中的版面布局以及信息分布。

&emsp;&emsp;简单点说,栅格布局,就是为了保证网页的统一性.使用栅格布局可以不考虑某个元素和其他元素的宽度,只需要考虑一个页面中有多少列栅格即可.

而在页面中,要实现栅格化的布局,方式有很多.如:
* 使用float加上相应的计算规则来实现栅格化布局.
* 使用flexBox弹性盒子,为子元素设置```flex:1 1 auto```来实现栅格化布局.
* 使用第三方库如:[Bootstrap](http://getbootstrap.com/)和[Foundation](http://foundation.zurb.com/)这类框架来实现栅格化布局.

&emsp;&emsp;然而,上述所示的几种方式虽然都可以实现栅格化的布局,但是却也都有着各自的缺点:
* 使用float来实现栅格化布局需要在CSS中进行大量的计算.
* 而flexBox是一维设计,它处理一个单独维度,一行或一列.我们不能为行和列创建一个严格的网格,这意味着如果使用flexbox网格我们还需要计算出浮动的百分比布局
* 使用如Bootstrap和Foundation则需要导入相应的库文件,而这样就会增加网页大小.降低浏览性能.

为了解决这些问题,CSS提供了原生的栅格化设计解决方```display: grid```

> CSS Grid布局，是一个基于网格的二维布局系统，目的是用来优化用户界面设计。

### 语法

```CSS
elementBox {
    display: grid;
    grid-template-columns: 1r 1r;
    grid-template-rows: 1r 1r;
}
```

上面代码中,使用```display: grid;```声明一个元素盒子为Grid布局.使用```grid-template-columns```和```grid-template-rows```指定Grid盒子中列的宽度和行的高度.同时也可以设置列和行的个数.这里,将Grid盒子设置为两行两列,并且宽度都是```1r```.其中```1r```为新的长度单位：fr单元,它表示在栅格容器中剩余的空间部分.

### Grid布局术语:
1. 网格容器（Grid Container）:
> 元素应用display:grid,它是其所有网格项的父元素.

2. 网格项（Grid Item）
> 网格容器的子元素.

3. 网格线（Grid Line）
> 组成网格线的分界线.它们可以是列网格线（column grid lines）,也可以是行网格线（row grid lines）并且居于行或列的任意一侧.

4. 网格轨道（Grid Track）
> 两个相邻的网格线之间为网格轨道,你可以认为它们是网格的列或行.

5. 网格单元（Grid Cell）
> 两个相邻的列网格线和两个相邻的行网格线组成的是网格单元，它是最小的网格单元。

6. 网格区（Grid Area）
> 网格区是由任意数量网格单元组成.

### 设置在网格容器上的属性

* display
* grid-template-columns
* grid-template-rows
* grid-template-areas
* grid-column-gap
* grid-row-gap
* grid-gap
* justify-items
* align-items
* justify-content
* align-content
* grid-auto-columns
* grid-auto-rows
* grid-auto-flow
* grid


### 浏览器兼容性
![这里写图片描述](http://img.blog.csdn.net/20170618020104173?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzYyNzY1Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



&emsp;&emsp;以上就是我对CSS 新特性之 Native Variables、 Feature Queries、Grid Layout的学习和理解.希望对大家有所帮助,如果文中有所不当之处,请予以斧正. 谢谢!


***参考资料***
1.  [CSS 变量教程](http://www.ruanyifeng.com/blog/2017/05/css-variables.html)
2. [Grid布局指南](http://www.jianshu.com/p/d183265a8dad)
3. [MDN 网格布局](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout)
4. [MDN @supports](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@supports)
5. [MDN 使用CSS变量](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_variables)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
