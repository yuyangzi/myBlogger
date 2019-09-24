---
title: Node那些事之认识node
date: 2017-3-18 09:27:37
tags:
- Node
- Node那些事系列
categories:
- 原创
- 前端技术
description: Node.JS是一个基于Chrome的V8引擎的JavaScript运行环境
---

## Node.JSd的起源
  [nodejs的历史由来](http://blog.csdn.net/u012028371/article/details/54884056)

  &emsp;&emsp;Node的创造者Ryan Dahl之前主要是做高性能服务器维护的.结合他的工作经验,他发现一个高性能的服务器主要有几个要点: **事件驱动** 、**非阻塞I/O**.  
  &emsp;&emsp;而Ryan Dahl基于这几个要点,分析了`C`,`C++`,`java`,`C#`,`JavaScript`几门语言.最终选择语言为`JavaScript`,解析器为V8引擎.

## 什么是Node.JS?

  * Node.JS是一个基于Chrome的V8引擎的JavaScript运行环境
    * 我们使用JS写的Node代码,就本质而言,只是单纯的字符串而已.不仅是Node,其他语言也是这样.计算机并不能直接的运行.
    * 而如果想要执行这些代码,就需要有一个解析执行环境.就Node而言,这个环境就是Chrome的V8引擎.

  * Node.JS使用了一个事件驱动,非阻塞式I/O的模型,使其轻量又高效
    * 事件驱动: 当触发某个指定的事件时,才会执行其指定的代码.否则永远不会执行.
    * 非阻塞式I/O的模型: 采用非阻塞的操作,可以有效的提高代码的执行效率.

  * Node.JS的包管理器npm,是全球最大的开源库生态系统.
    * npm的使用流程:
    1. 使用`npm init -y`初始化一下把管理文件`package.json`,将来所有安装的包都会记录到`package.json`文件当中去.
    2. 使用`npm install [包名] --save/--save-dev`安装你所需要的包.其中,`install`可以简写成`i`.
    3. `--save`表示把包安装到部署依赖当中(在开发和部署上线都需要使用的包).`--save-dev`表示把包安装到开发依赖当中(只有在开发时才会使用到的包).
    4. `--save`可以简写成`-S`.`--save-dev`可以简写成`-D`.
    5. 使用`npm uninstall [包名] --save/--save-dev`或`npm remove [包名] --save/--save-dev`即可卸载指定的包.
    6. 使用`npm i [包名] -g`即可将包安装到全局环境中.这样在任何地方都可以使用.

  * NPM 和 GitHub与GIt之间的关系
    * NPM是包管理平台(里面托管了各种各样的包).
    * GitHub是代码托管平台(托管了各种各样的代码创库).
    * GIt使用来对代码进行分支管理的.

  * 什么是Yarn
    * Yarn和npm的作用完全一样,都是用来管理项目中的第三方依赖包的.
    * Yarn是Facebook公司推出来的一套包管理工具.在使用React和ReactNative的时,默认就是用Yarn来安装其依赖包的.
    * Yarn会缓存曾经安装过的包,虽然npm也会缓存,但其效果不如Yarn明显.

  * 设置npm和Yarn的国内淘宝镜像
    **什么是淘宝镜像:** 由于npm和Yarn的服务器在国外,所以每次安装包的时候需要走国外的网络,速度很慢.因此,淘宝帮助我们在国内建立了镜像.来帮助我们提高安装包的速度.

    1. 配置npm的国内淘宝镜像:

    ```
    npm config set registry https://registry.npm.taobao.org --global
    npm config set disturl https://npm.taobao.org/dist --global
    ```

    2. 配置Yarn的国内淘宝镜像:

    ```
    yarn config set registry https://registry.npm.taobao.org --global
    yarn config set disturl https://npm.taobao.org/dist --global
    ```


## Node.JS可以做哪些事:
  * Node可以作为一个工具区使用,通过NPM安装需要的包,然后用Node.JS区使用它.
  * Node可以做服务器开发.
  * Node还可以操作数据库(如MongoDB)并对外提供数据接口.
  * Node可以做及时聊天.

## Node中执行相关的JS代码的两种方式
  1. 直接在命令行中输入`node`,进入Node的`REPL`运行环境:
    * R: Read -读取用户输入的字符串.
    * E: Evaluate -把用户输入的字符串,当做JS代码去解析.
    * P: Print -打印输出Evaluate解析的结果.
    * L: Loop -进入下一次循环.

  2. 将Node代码写入到一个JS文件中,然后通过`node [要执行的JS文件]`去运行Node代码.


**总结:Node.js就是一个JavaScript的服务端运行环境(依赖Chrome的v8引擎),为JavaScript提供了服务端编程的能力**


**我的个人网址:** [http://www.wangyiming19950222.com](http://www.wangyiming19950222.com)

## 相关文章
+ [深入浅出Node.js（一）：什么是Node.js](http://www.infoq.com/cn/articles/what-is-nodejs/)
+ [I/O事件概述](http://www.cnblogs.com/eric-nirnava/p/IO.html)
+ [理解Node.js事件驱动编程](http://www.cnblogs.com/lua5/archive/2011/02/01/1948760.html)
+ [Node.js 中文网](http://nodejs.cn/api/documentation.html)
+ [Node.js 英文官网](https://nodejs.org/en/)
+ [Node.js 菜鸟教程](http://www.runoob.com/nodejs/nodejs-tutorial.html)
+ [CNode：Node.js专业中文社区](https://cnodejs.org/)
+ [深入浅出Node.js(朴灵)完整扫描版.pdf](http://download.csdn.net/download/u010733705/9555797)
+ [HTTP Keep-Alive是什么？如何工作？](http://www.nowamagic.net/academy/detail/23350305)
+ [浏览器User-agent String里的历史故事](http://www.nowamagic.net/librarys/veda/detail/2576)
+ [art-template API](https://aui.github.io/art-template/docs/api.html)
+ [yarn与npm的命令行小结](http://www.jb51.net/article/95199.htm)

**我的个人网址:** [https://wangyiming.info/](https://wangyiming.info/)
