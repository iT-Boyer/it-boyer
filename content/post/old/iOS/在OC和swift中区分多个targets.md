---
title: 在OC和swift中区分多个targets
date: 2017-01-11 13:49:33
updated: 2017-08-17 14:54:01
comments: true
tags: [xcode]
categories:
- 解决方案
keywords: Flags,DEVELOPMENT,osx
permalink: 
---

## build setting预编译位置 
1. Preprocessor Macros
2. Other Swift Flags

为生产和开发target配置预处理宏/编译器标识。之后我们就可以使用该标识在我们的代码来检测应用程序正在运行的版本。

### Objective-C项目中Preprocessor Macros  
#### 配置
---
添加位置：选择项目中对应的target名->在`Build Settings`下`Apple LLVM 7.0 - Preprocessing`->`Preprocessor Macros`。
添加变量：在Rebug和Release区域添加一个变量`DEVELOPMENT`
    对应target1: 设`DEVELOPMENT=1`表示开发版
    对应target2: 设`DEVELOPMENT=0`表示生产版
#### 使用
---
根据已配置的宏DEV_VERSION，我们可以在代码中利用它动态地编译项目。下面是一个简单的例子：
Objective-C中使用`＃if`检查`DEVELOPMENT`的环境，并相应的设置URLs/ API密钥。

``` objc
#if DEVELOPMENT
     #define SERVER_URL @"http://dev.server.com/api/"
     #define API_TOKEN @"DI2023409jf90ew"
#else
     #define SERVER_URL @"http://prod.server.com/api/"
     #define API_TOKEN @"71a629j0f090232"
#endif
```

### Swift中Other Swift Flags
对于swift的项目，编译器不再支持预处理指令。作为替代，它使用编译时的属性和build配置。
#### 配置
---
选中开发target，添加一个标识表示开发版本
选中`target` -> `Build Setting`->`Swift Compiler - Custom Flags`->将值设为`-DDEVELOPMENT`表示这个target作为开发版本。
#### 使用
---
Swift中你仍然可以使用`#if`判定build的参数动态编译。然而，除了使用`#define`定义基本常量，在swift中我们也可以用`let`定义一个全局常量。

``` swift
#if DEVELOPMENT
let SERVER_URL = "http://dev.server.com/api/"
let API_TOKEN = "DI2023409jf90ew"
#else
let SERVER_URL = "http://prod.server.com/api/"
let API_TOKEN = "71a629j0f090232"
#endif
```
[参照](http://www.cocoachina.com/ios/20160331/15832.html)
