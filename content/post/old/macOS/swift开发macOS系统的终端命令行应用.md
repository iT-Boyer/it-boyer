---
title: swift开发macOS系统的终端命令行应用
date: 2017-05-29 13:00:08
updated: 2017-05-29 13:00:08
comments: true
tags: [工具,macOS]
categories:
- 学习笔记
keywords: 
permalink: 
---


### 教程
[命令行程序MacOS的教程](https://www.raywenderlich.com:443/128039/command-line-programs-macos-tutorial)

使用swift创建一个命令行程序，相比其他语言如C、Perl、Ruby或java。
选用SWIFT的理由：
Swift可以用作解释脚本语言，也可以用作编译语言。这使您具有脚本语言的优势，如零编译时间和易于维护，以及选择编译应用程序，以提高执行时间或捆绑出售给公众。

### main.swift主体
许多C语言的main函数作为切入点，例如当操作系统调用这个程序时执行的代码入口。这意味着程序的执行始于这个函数的第一行。
Swift没有一个main函数，而是main.swift文件。这样在运行Swift项目时，直接运行的事main.swift文件，执行入口开始于第一行代码。

### 终端调用程序
命令行可分两种模式
* 静态可执行模式：通过终端app直接运行命令行工具，执行固有功能。
* 交互命令行模式 ：需要用户通过使用说明信息，对命令行程序输入交互命令，执行相应的功能。

1. 在同一个目录下执行
```
./Panagram
```
2. 相对路径执行
```
Debug/Panagram
```




