---
title: makefile自动化编译工具
date: 2018-11-08 20:30:54
updated: 2018-11-08 20:30:54
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---

make命令执行时，需要一个makefile文件，以告诉make命令需要怎么样的去编译和链接程序。

首先，我们用一个示例来说明makefile的书写规则，以便给大家一个感性认识。这个示例来源于gnu 的make使用手册，在这个示例中，我们的工程有8个c文件，和3个头文件，我们要写一个makefile来告 诉make命令如何编译和链接这几个文件。我们的规则是：

如果这个工程没有编译过，那么我们的所有c文件都要编译并被链接。
如果这个工程的某几个c文件被修改，那么我们只编译被修改的c文件，并链接目标程序。
如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的c文件，并链接目标程序。
只要我们的makefile写得够好，所有的这一切，我们只用一个make命令就可以完成，make命令会自动智能 地根据当前的文件修改的情况来确定哪些文件需要重编译，从而自动编译所需要的文件和链接目标程序。

makefile的规则

在讲述这个makefile之前，还是让我们先来粗略地看一看makefile的规则。
[跟我一起写Makefile](https://seisman.github.io/how-to-write-makefile/introduction.html)

