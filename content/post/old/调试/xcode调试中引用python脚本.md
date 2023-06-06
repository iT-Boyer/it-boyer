---
title: xcode调试中引用python脚本
date: 2018-10-17 19:47:15
updated: 2018-10-17 19:47:48
comments: true
tags: [调试,lldb]
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

Xcode集成了LLDB，进一步简化了程序调试流程。虽然LLDB很强大，但是它的命令很有限。所幸的是，lldb包含了对python的支持，使得lldb的拓展成为可能。本人在开发过程中很喜欢使用image lookup 命令，但是苦于每次只能执行一条，相当耗时，因此一直想要找到一种批量执行的方法

## 问题
批量执行image lookup -a
`optparse`和`shlex`是用于解析参数的两个重要的库。通过`optparse`来生成解析器
1. 编写python脚本(layne_command.py)，代码如下：
```python
#coding=utf-8  
#自定义lldb命令 
import lldb
import commands
import optparse
import shlex

#批量执行`image lookup`命令的函数，也是自定义的新的lldb命令的名称
def layne_imagelookup(debugger, command, result, internal_dict):
    target = debugger.GetSelectedTarget()
    process = target.GetProcess()
    thread = process.GetSelectedThread()

    command_args = shlex.split(command)
    parser = create_custom_parser()

    try:
        (options, args) = parser.parse_args(command_args)
    except:
        result.SetError ("option parsing failed")
        return
    if args:
        for address in args:
            print("*************************************")
            debugger.HandleCommand('image lookup -a %s'%(address))


def create_custom_parser():
    usage = "usage: %prog [options]"
    description = '''Parse Symbols to Human-readable Format.'''
    #生成解析器
    parser = optparse.OptionParser(description=description, prog='print_frame',usage=usage)
    # parser.add_option('-p','--parse',type='string',dest = 'parse',help='parse symbols.');
    return parser

#运行脚本入口
def __lldb_init_module(debugger, internal_dict):  
    debugger.HandleCommand('command script add -f layne_command.layne_imagelookup layne_imagelookup')  
    print('The "layne_imagelookup" python command has been installed and is ready for use.')
```
然后保存为文件`layne_command.py`,放到如下目录(自己指定)：`~/Python/lldb/layne_command.py `
说明: 
①#coding=utf-8指定python脚本编码，否则运行时注释中的中文将会报错。 
②运行脚本时入口为` __lldb_init_module(debugger,internal_dict)` 。`debugger.HandleCommand`是`python`中执行`lldb`命令的主要方式。 
③`layne_imagelookup`是批量执行`image lookup`命令的函数，也是自定义的新的lldb命令的名称。 
④`optparse`和`shlex`是用于解析参数的两个重要的库。通过`optparse`来生成解析器。
