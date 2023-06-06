---
title: Chisel-xcodeproj框架的使用
date: 2018-11-09 13:05:46
updated: 2018-11-09 20:13:28
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
{% github it-boyer chisel f331dc6 width = 30% %}
这个pull请求添加`findinstances命令`，该命令完成[Add findinstances, and new support framework in Chisel.xcodeproj ](https://github.com/facebook/chisel/pull/197)和[Add Makefile for installing framework](https://github.com/facebook/chisel/pull/214)的工作。
用户可以运行`help findinstances`获取`findinstances`的详细信息。简要总结一下，`findinstances`可以找到给定`class`类或`protocol`协议的所有实例，并使用谓词表达式过滤这些结果。
如果您有一个名为`XXSocialUser`的类，那么您可以通过运行`findinstances XXSocialUser == 'curry'`来找到一个特定的用户。

`Chisel.xcodeproj`为新建`findinstance`提供了凿子框架支持。
使用`Chisel.xcodeproj`支持新建`chisel Framework`通过本地代码实现`command`,`findinstances`等功能。也可以通过这种方式来实现更多`chisel命令`。

`findinstance`命令通过扫描**iOS/macOS**`malloc API`。对于每个`allocation`分配，都会使用`heuristics`来识别可能的`Objective-C实例`。`heuristics`不调用对象上的method，而是依赖`objc runtime`运行时函数,基于`class metadata`类元数据来匹配到`oc实例`。这避免了在`objc运行时`机制下分配和有状态副作用。
在第一次传递之后，候选对象将通过第二次传递，检查它们是否与可选的`NSPredicate`匹配。如果没有谓词，则输出对象的信息最少。如果有谓词，并且对象传递谓词，那么对象将输出更多细节，特别是谓词中查询的细节。
```
findinstances UIView
findinstances *UIView
findinstances UIScrollViewDelegate
findinstances UIView window == nil || hidden == true || alpha == 0 || layer.bounds.#size.width == 0 ||  layer.bounds.#size.height == 0 
findinstances UIView subviews.@count == 0
findinstances NSDictionary any @allKeys beginswith 'perf_'
findinstances NSArray @count > 100
```
### 开发使用
构建Xcode项目，并获得到`Chisel Framework`的路径。:
```
/Users/<me>/Library/Developer/Xcode/DerivedData/Chisel-<stuff>/Build/Products/Debug-iphonesimulator/Chisel.framework/Chisel
```
在lldb环境下执行：
```
$ lldb
>>> expr -l objc -- (void*)dlopen("/path/to/Chisel.framework/Chisel", 2)
script o=lldb.SBExpressionOptions(); o.SetLanguage(lldb.eLanguageTypeObjC); o.SetTrapExceptions(False); o.SetTryAllThreads(False); o.SetTimeoutInMicroSeconds(10*1000000); lldb.frame.EvaluateExpression('(void)PrintInstances("<classname>", "<predicate>")', o)
```
`<classname>`:可以是`class`类名或`protocol`协议名
`<predicate>`:是一个可由`NSPredicate`解析的字符串
可以使用`regex command 命令`:
(注意，`findinstance`后面必须换行) 
```
command regex findinstances
s/(\S+) *(.*)/script o=lldb.SBExpressionOptions(); o.SetLanguage(lldb.eLanguageTypeObjC); o.SetTrapExceptions(False); o.SetTryAllThreads(False); o.SetTimeoutInMicroSeconds(10*1000000); lldb.frame.EvaluateExpression('(void)PrintInstances("%1", "%2")', o)/
```
或者，作为`python命令`，存储在`path/to/findinstances.py`中:
```
import lldb

def findinstances(debugger, command, exe_ctx, result, _):
options = lldb.SBExpressionOptions()
options.SetTrapExceptions(False)
options.SetTryAllThreads(False)
options.SetTimeoutInMicroSeconds(10*1000000)
options.SetLanguage(lldb.eLanguageTypeObjC)

frame = exe_ctx.frame

if not exe_ctx.target.module['Chisel']:
frame.EvaluateExpression('(void*)dlopen("/path/to/Chisel.framework/Chisel", 2)', options)

args = command.split(' ', 1)
typeName = args[0]
predicate = args[1] if len(args) > 1 else ''
frame.EvaluateExpression('(void)PrintInstances("{}", "{}")'.format(typeName, predicate), options)

def __lldb_init_module(debugger, _):
debugger.HandleCommand('command script add -f findinstances.findinstances findinstances')
```

### 安装
[Add Makefile for installing framework](https://github.com/facebook/chisel/pull/214)
This allows you to run make install with optional environment variables
in order to build and install Chisel.framework.
```
PREFIX ?= /usr/local/lib

export INSTALL_NAME =
ifneq ($(LD_DYLIB_INSTALL_NAME),)
    INSTALL_NAME = "LD_DYLIB_INSTALL_NAME=$(LD_DYLIB_INSTALL_NAME)"
endif

install:
    xcodebuild \
        -scheme Chisel \
        -configuration Release \
        -sdk iphonesimulator \
        install \
        $(INSTALL_NAME) \
        DSTROOT=/ \
        INSTALL_PATH="$(PREFIX)"

```
