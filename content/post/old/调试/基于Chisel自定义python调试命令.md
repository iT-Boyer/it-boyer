---
title: 基于Chisel自定义python调试命令
date: 2018-10-21 09:03:35
updated: 2018-10-21 09:03:35
comments: true
tags: [iOS,调试]
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
<!--github库卡片-->
{% github it-boyer chisel 56d0e0d width = 30% %}
## 管理.lldbinit
.lldbinit每次启动Xcode 都会加载lldb环境，故当自定义命令写好之后，可以通过这里加载。
```
script fblldb.loadCommandsInDirectory('/magical/commands/')
```
现在将lldbinit原文件放在自己便于管理的地方，便于导入python命令
```sh
#!/bin/sh

#  installChisel.sh
#  HexoDeploy
#
#  Created by admin on 2018/10/21.
#  Copyright © 2018年 boyer. All rights reserved.
basepath=$(cd `dirname $0`; pwd)
echo "当前cd的目录名："`basename $(pwd)`
echo "sh脚本文件的绝对路径：$basepath"
ln -fs $basepath/lldbinit ~/.lldbinit
echo "done"
```
## 创建加载自定义命令
无论是本地使用还是提交到`Chisel`贡献，工作流程都是一样的。
1. 在便于管理的目录下，新建python文件 `vi example.py`，在chisel会通过该目录路径来加载新命令：
```
#!/usr/bin/python
# Example file with custom commands, located at /magical/commands/example.py

import lldb
import fblldbbase as fb

def lldbcommands():
    return [ PrintKeyWindowLevel() ]

class PrintKeyWindowLevel(fb.FBCommand):
    def name(self):
        return 'pkeywinlevel'

def description(self):
    return 'An incredibly contrived command that prints the window level of the key window.'

def run(self, arguments, options):
    # It's a good habit to explicitly cast the type of all return
    # values and arguments. LLDB can't always find them on its own.
    lldb.debugger.HandleCommand('p (CGFloat)[(id)[(id)[UIApplication sharedApplication] keyWindow] windowLevel]')
```
2. 加载新建的命令 
可以使用`Chisel`提供的函数`loadCommandsInDirectory`加载，在`fblldb.py`中声明的方法。
在`~/.lldbinit`新增如下：
```
# ~/.lldbinit
...
command script import /path/to/fblldb.py
script fblldb.loadCommandsInDirectory('/magical/commands/')
```
> 更方便的方式：命令文件直接放在chisel源码中的`mcommands`目录中，这样会自动加载，就不用在lldbinit中配置了。
因为内置的支持，新命令也可以轻松使用`arguments`参数和`options`选项的相关功能。请参阅`border`和`pinvocation`命令的用法。


## 开发调试命令的流程
无论是本地使用还是提交到`Chisel`贡献，都是相同的工作流。
1. 启动`LLDB`
2. 拦截断点(或者通过Xcode的调试栏中的`pause`按钮暂停执行，或者直接`process interrupt`进程中断)
3. 执行命令`source ~/.lldbinit`，在LLDB中提供命令源
4. 运行您正在执行的命令
5. 修改命令
6. 重新加载脚本`script reload(modulename)`
7. 重复3-6步骤，直到自定义命令达到预期效果。
