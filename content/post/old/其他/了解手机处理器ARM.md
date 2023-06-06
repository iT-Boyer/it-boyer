---
title: 了解手机处理器ARM
date: 2018-10-15 08:04:57
updated: 2018-10-15 08:26:32
comments: true
tags: [iOS]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
Arm处理器，因为其低功耗和小尺寸而闻名，几乎所有的手机处理器都基于arm，其在嵌入式系统中的应用非常广泛，它的性能在同等功耗产品中也很出色。

Armv6、armv7、armv7s、arm64都是arm处理器的指令集，所有指令集原则上都是向下兼容的，如iPhone4S的CPU默认指令集为armv7指令集，但它同时也兼容armv6指令集，只是使用armv6指令集时无法充分发挥其性能，即无法使用armv7指令集中的新特性，同理，iPhone5的处理器标配armv7s指令集，同时也支持armv7指令集，只是无法进行相关的性能优化，从而导致程序的执行效率没那么高。


[iOS armv7, armv7s, arm64区别与应用32位、64位配置](https://www.jianshu.com/p/567d3b730608)
[为什么手机多用arm？](https://blog.csdn.net/hello_haozi/article/details/8073107)
