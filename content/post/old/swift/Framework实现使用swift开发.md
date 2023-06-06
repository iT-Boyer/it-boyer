---
title: Framework实现使用swift开发
date: 2018-06-23 16:41:58
updated: 2019-01-16 21:01:14
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github it-boyer JHHomeAPP 813e4b width = 30% %}
## 在静态库组件中使用swift开发
基于静态库的开发，实现封装静态库/Framework并使用swiftOC混编开发
### 创建三个角色
1. 主项目：`JHHomeAPP`
2. 静态库组件：`FirstPlug` 
    * `SwiftInStaticLib`(包含Swift源码实现的静态库)
3. 动态库`secondFramework`

### 静态库：问题1
当静态库组件中存在swift源码时，依赖该组件的主工程会报错：
```
ld: warning: Auto-Linking library not found for -lswiftDispatch
ld: warning: Auto-Linking library not found for -lswiftCoreFoundation
ld: warning: Auto-Linking library not found for -lswiftObjectiveC
ld: warning: Auto-Linking library not found for -lswiftDarwin
ld: warning: Auto-Linking library not found for -lswiftFoundation
ld: warning: Auto-Linking library not found for -lswiftCoreGraphics
ld: warning: Auto-Linking library not found for -lswiftCore
ld: warning: Auto-Linking library not found for -lswiftSwiftOnoneSupport
```
1. 联想方法：
设置 `Always Embed Swift Standard Libraries`: `YES`
结果无效。
2. 适用的解决方法
在主工程中新建一个空的swift源文件，不需要自动新建`$(SWIFT_MODULE_NAME)-Swift.h`映射文件和`JHHomeAPP/JHHomeAPP-Bridging-Header.h`头文件。
**使用方法2，问题1就不存在了，证明了在静态库中可以使用swift源码文件进行开发,同样证明了静态库可以封装包含swift源码的静态库。**

### Framework：问题2
1. 在动态库中objc源码方法可以封装到静态库，并在可执行文件中调用。
2. 当在Framework中新建swift源文件时，第一次编译运行出现崩溃问题：
```
dyld: Library not loaded: @rpath/libswiftCoreImage.dylib
Referenced from: .../../Debug-iphonesimulator/SecondFramework.framework/SecondFramework
Reason: image not found
```
解决办法：需要在`ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES`设置为`YES`.再次编译出现问题3。
### Framework: 问题3
当在oc源码文件中用引用`-Swift.h`头文件时，出现一下问题:
```
'SecondFramework-Swift.h' file not found
#import "SecondFramework-Swift.h"
^~~~~~~~~~~~~~~~~~~~~~~~~
```
结果将：`Install Objective-C Compatibility Header` : `NO`可以正常调用swift方法了。

>验证：framework可以封装到静态库中，当有swift源码实现时需要设置ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES=YES

### 总结
1. Product Module Name: 该项默认为项目名或自定义的名称
2. Defines Module: 设置为YES （framework默认为YES，静态库默认为NO）
2. Embedded Content Contains Swift: 设置为YES
3. Install Objective-C Compatibility Header：设置为YES （如上题说：在framework中设置为NO，才能正常运行，在静态库中似乎不影响）
4. Objective-C Bridging Header: 自定义需要桥接到Swift中的OC头文件（EX：$(SRCROOT)/Swift-Bridging-Header.h）





