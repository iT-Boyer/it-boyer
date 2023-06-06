---
title: Android安装BusyBox
date: 2018-10-15 08:10:09
updated: 2018-10-15 08:26:32
comments: true
tags: []
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

大家是否有过这样的经历，在命令行里输入adb shell，然后使用命令操作你的手机或模拟器，但是那些命令都是常见Linux命令的阉割缩水版，用起来很不爽。是否想过在Android上使用较完整的shell呢？用BusyBox吧。不论使用adb连接设备使用命令行还是在手机上直接用terminal emulator都可以。

## 什么是BusyBox ？

BusyBox 是标准 Linux 工具的一个单个可执行实现。BusyBox 包含了一些简单的工具，例如 cat 和 echo，还包含了一些更大、更复杂的工具，例如 grep、find、mount 以及 telnet。有些人将 BusyBox 称为 Linux 工具里的瑞士军刀.简单的说BusyBox就好像是个大工具箱，它集成压缩了 Linux 的许多工具和命令。（摘自百度百科）

## 在Android上安装BusyBox

准备：

0. 先要把手机给Root了，具体教程这里就不提供了，网上有很多。

1. 下载BusyBox的binary，打开这个地址 http://www.busybox.net/downloads/binaries ，选择最新版本，然后下载对应你的设备架构的版本，这里我下载了busybox-armv6l，下面将以这个文件名为示例。
[[Android] 为Android安装BusyBox —— 完整的bash shell](http://www.cnblogs.com/xiaowenji/archive/2011/03/12/1982309.html)
