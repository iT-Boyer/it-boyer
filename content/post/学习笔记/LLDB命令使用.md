---
title: LLDB命令使用
date: 2018-08-31 16:20:20
updated: 2018-10-17 19:47:48
comments: true
tags: [调试,lldb]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github it-boyer  width = 30% %}

### help
最简单命令是 help，它会列举出所有的命令。如果你忘记了一个命令是做什么的，或者想知道更多的话，你可以通过 help <command> 来了解更多细节，例如 help print 或者 help thread。如果你甚至忘记了 help 命令是做什么的，你可以试试 help help。不过你如果知道这么做，那就说明你大概还没有忘光这个命令。
### 打印变量
可以给 print 指定不同的打印格式。它们都是以 `print/<fmt>` 或者简化的 `p/<fmt>` 格式书写。
下面是一些例子：
默认的格式:
```
(lldb) p 16
16
```
十六进制:
```
(lldb) p/x 16
0x10
```
二进制 (t 代表 two)：
```
(lldb) p/t 16
0b00000000000000000000000000010000
(lldb) p/t (char)16
0b00010000
```
你也可以使用 `p/c` 打印字符，或者 `p/s` 打印以空终止的字符串 (译者注：以 '\0' 结尾的字符串)。
[这里](https://sourceware.org/gdb/onlinedocs/gdb/Output-Formats.html)是格式的完整清单。
### 完全在调试器内运行
在开始舞蹈之前，还有一件事要看一看。实际上你可以在调试器中执行任何 `C/Objective-C/C++/Swift` 的命令。唯一的缺点就是不能创建新函数... 这意味着不能创建新的类，block，函数，有虚拟函数的 C++ 类等等。除此之外，它都可以做。
