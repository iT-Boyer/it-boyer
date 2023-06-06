---
title: JavaScript自动化组件OC桥接
date: 2017-02-15 17:19:30
updated: 2017-02-16 13:43:01
comments: true
tags: []
categories:
- 学习笔记
keywords: 工具,搭建,语法,测试,管理,混编
permalink: 
---
[文档](https://developer.apple.com/library/content/releasenotes/InterapplicationCommunication/RN-JavaScriptForAutomation/Articles/OSX10-10.html#//apple_ref/doc/uid/TP40014508-CH109-SW8)
`JavaScript自动化`有一个内置的`Objective-C Bridge`，使您能够访问文件系统，创建Cocoa应用程序。
`Objective-C Bridge`的主要接入点是全局属性`objc`和`$`。
## Frameworks
`Foundation framework`中的语法默认支持`JavaScript自动化`。也可以通过使用`ObjC.import()`方法导入其他Frameworks 和 libraries。
例如，使用`Cocoa框架`中的`NSBeep()`函数，需要导入`Cocoa框架`
{% codeblock lang:js %}
ObjC.import('Cocoa')
$.NSBeep()
{% endcodeblock %}
除了系统框架之外，一些系统库的功能也被暴露出来。这个功能可以通过`头文件`的名称来暴漏出来（不带.h）
例如：  
arpa/inet, asl, copyfile, dispatch, dyld, errno, getopt, glob, grp, ifaddrs, launch, membership, netdb, netinet/in, notify, objc, paths, pwd, readline, removefile, signal, spawn, sqlite3, stdio, stdlib, string, sys/fcntl, sys/file, sys/ioctl, sys/mount, sys/param, sys/resource, sys/socket, sys/stat, sys/sysctl, sys/time, sys/times, sys/types, sys/wait, sys/xattr, syslog, time, unistd, uuid/uuid, vImage, vecLib, vmnet, xpc, 和 zlib.
导入框架时，系统将参考桥接支持文件。除了内置的框架和库，您可以导入任何具有桥接支持的框架，只需要将完整路径传递给框架，如下示例：
{% codeblock lang:js %}
ObjC.import('/Library/Frameworks/Awesome.framework')
{% endcodeblock %}

## 数据类型
原始的`JavaScript数据类型`映射到`C数据类型`。例如，一个`JavaScript字符串`映射为`char *`，而`JavaScript整数`映射到`int`。使用`objc API`返回一个`char *`时，会得到一个`JS 字符串`

原始的`JavaScript数据类型`将被自动转换为`ObjC对象类型`，并能作为一个预期的对象类型的参数传递给ObjC方法。
例如，一个`JS字符串`将被转换为一个`NSString对象`如果是什么方法签名说应该是输入。
> 注意，然而，ObjC方法返回的ObjC对象类型是不会自动转换为原始的JavaScript的数据类型。

## 实例化的类和调用方法
所有类都定义为`$对象`的属性。ObjC对象的方法有两种方式调用，根据是否需要参数的方法。
如果ObjC方法不带参数，然后调用`JavaScript属性名`访问`属性值`。这个例子中实例化一个空的字符串。
{% codeblock lang:js %}
str = $.NSMutableString.alloc.init
{% endcodeblock %}
如果ObjC方法不带参数，根据`JSExport`规范来命名，通过JavaScript的方法调用（function-typed property）；
对于多参数的方法，Objective-C的方法每个部分都合并在一起，冒号后的字母变为大写并移除冒号。比如下边协议中的方法，在JavaScript调用就是：doFooWithBar(foo, bar);
这个例子说明`JavaScript字符串`转为`NSString`然后写入到一个文件
{% codeblock lang:js %}
str = $.NSString.alloc.initWithUTF8String('foo')
str.writeToFileAtomically('/tmp/foo', true)
{% endcodeblock %}

如果你调用一个方法，如`-intValue`，返回`C数据类型`而不是一个对象，然后你会回到原始的`JavaScript数据类型`。
此示例返回原始的JavaScript的整数，99。
{% codeblock lang:js %}
$.NSNumber.numberWithInt(99).intValue
{% endcodeblock %}

### 访问 ObjC Properties

`ObjC属性`也可以通过`JavaScript属性`来访问，很像调用无参数方法。
当一个`桥接对象属性`的被访问时，ObjC属性列表是第一参考，如果列表中存在该名称对应的属性，那么就调用相应属性的`getter`或`setter`选择器。如果该名称的ObjC属性不在类中属性的列表中，那么该属性名称就作为`方法选择器`来调用。
使用自定义`getter`名定义一个属性，你可以使用`属性`名 或 `getter`名，并得到相同的结果。
{% codeblock lang:js %}
task = $.NSTask.alloc.init
task.running == task.isRunning
{% endcodeblock %}
另外，不同的参数方法，`桥接对象属性`映射到`ObjC属性`也可以设置为（read/write属性）。下面的两行定义了一个ObjC属性：`launchPath`。
{% codeblock  lang:js %}
task.launchPath = '/bin/sleep'
task.setLaunchPath('/bin/sleep')
{% endcodeblock %}

