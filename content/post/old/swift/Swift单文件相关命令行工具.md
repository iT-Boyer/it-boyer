---
title: Swift单文件相关命令行工具
date: 2018-10-01 21:02:49
updated: 2018-10-01 23:57:27
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

## 直接用 swift 命令执行

`xcrun swift`可以直接将一个 `.swift`文件作为命令行工具的输入，这样里面的代码也会被自动地编译和执行。我们甚至还可以在 `.swift` 文件最上面加上命令行工具的路径，然后将文件权限改为可执行，之后就可以直接执行这个 .swift 文件了：
1. hello.swift
```sh
#!/usr/bin/env xcrun swift 
println("hello") 
```
2. 终端设置为可执行权限，并运行打印：
```sh
> chmod 755 hello.swift 
> ./hello.swift 
// 输出： 
hello 
```
>这些特性与其他的解释性语言表现得完全一样

## swiftc
相对于直接用 swift 命令执行，Swift 命令行工具的另一个常用的地方是直接脱离 Xcode 环境进行编译和生成可执行的二进制文件。我们可以使用 swiftc 来进行编译，比如下面的例子：
1. MyClass.swift
```swift
class MyClass { 
    let name = "XiaoMing" 
    func hello() { 
        println("Hello \(name)") 
        } 
} 
```
2. main.swift
```swift 
let object = MyClass() 
object.hello() 
```
3. 终端编译运行，将生成一个名叫 `main` 的可执行文件：
```sh
> xcrun swiftc MyClass.swift main.swift
> ./main  //运行main文件
```
利用这个方法，我们就可以用 Swift 写出一些命令行的程序了。

最后想说明的一个 Swift 命令行工具的使用案例是生成汇编级别的代码。有时候我们想要确认经过优化后的汇编代码实际上做了些什么。swiftc 提供了参数来生成 asm 级别的汇编代码：
```sh
swiftc -O hello.swift > hello.asm
```
Swift 的命令行工具还有不少强大的功能，对此感兴趣的读者不妨使用 `xcrun swift --help` 以及 `xcrun swiftc --help` 来查看具体还有哪些参数可以使用。

## `swiftc -g`支持lldb调试

