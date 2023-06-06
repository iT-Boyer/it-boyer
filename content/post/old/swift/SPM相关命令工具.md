---
title: SPM相关命令工具
date: 2018-10-01 14:52:23
updated: 2018-10-01 23:57:27
comments: true
tags: [swift]
categories:
- 学习笔记
---

## 概念概述 
[getting-started](https://swift.org/getting-started)
{% github it-boyer PerfectTemplate 20294e56 width = 30% %}
## 使用Swift编译系统
Swift 编译系统为编译库、可执行文件和在不同工程之间共享代码提供了基本的约定。
### swift 工具
* swift package
* swift package generate-xcodeproj
* swift run
* swift build
* swift test
 
### `swift package`创建一个`Hello`SPM
1. 创建`Hello`目录，目录名会作为SPM名称：
```
$ mkdir Hello
$ cd Hello
```
2. `swift package`初始化为SPM工程
```sh
$ swift package init
├── Package.swift   // 依赖清单文件
├── README.md
├── Sources         // 源码目录
│   └── Hello
│       └── Hello.swift
└── Tests
├── HelloTests
│   └── HelloTests.swift
└── LinuxMain.swift
```
每个包在其根目录下都必须拥有一个命名为`Package.swift`清单文件。如果清单文件为空，那包管理器将会使用常规默认的方式来编译包。
3.  `swift build`编译SPM
编译会先解析`Package.swift`项目配置和下载依赖库等环境，然后编译源码
```sh
$ swift build  #编译可执行文件
Compile Swift Module 'Hello' (1 sources)
```
4. swift test运行SPM的单元测试
```sh
$ swift test
Compile Swift Module 'HelloTests' (1 sources)
Linking ./.build/x86_64-apple-macosx10.10/debug/HelloPackageTests.xctest/Contents/MacOS/HelloPackageTests
Test Suite 'All tests' started at 2016-08-29 08:00:31.453
Test Suite 'HelloPackageTests.xctest' started at 2016-08-29 08:00:31.454
Test Suite 'HelloTests' started at 2016-08-29 08:00:31.454
Test Case '-[HelloTests.HelloTests testExample]' started.
Test Case '-[HelloTests.HelloTests testExample]' passed (0.001 seconds).
Test Suite 'HelloTests' passed at 2016-08-29 08:00:31.455.
Executed 1 test, with 0 failures (0 unexpected) in 0.001 (0.001) seconds
Test Suite 'HelloPackageTests.xctest' passed at 2016-08-29 08:00:31.455.
Executed 1 test, with 0 failures (0 unexpected) in 0.001 (0.001) seconds
Test Suite 'All tests' passed at 2016-08-29 08:00:31.455.
Executed 1 test, with 0 failures (0 unexpected) in 0.001 (0.002) seconds
```
### 创建一个可执行的SPM
可运行的SPM必须包含`main.swift`文件。SPM会把`main.swift`编译成可执行文件的二进制文件。
在本例中，SPM将生成一个名为`Hello`的可执行文件，输出“Hello, world!”
1. 创建
```sh
$ mkdir Hello
$ cd Hello
$ swift package init --type executable
```
2. 两种运行方式
2.1 先编译在运行
```
$ swift build
Compile Swift Module 'Hello' (1 sources)
Linking ./.build/x86_64-apple-macosx10.10/debug/Hello

$ .build/x86_64-apple-macosx10.10/debug/Hello
Hello, world!
```
2.2 `swift run`直接运行
```sh
$ swift run Hello
Compile Swift Module 'Hello' (1 sources)
Linking ./.build/x86_64-apple-macosx10.10/debug/Hello
Hello, world!
```
3. 多个源文件协作
下一步，让我们在新的资源文件里定义一个新的方法 `sayHello(_:)` 然后直接用 `print(_:)` 替换执行调用的内容。
在 `Sources/` 目录下创建一个新文件命名为 `Greeter.swift` 然后输入如下代码：
```swift
func sayHello(name: String) {
    print("Hello, \(name)!")
}
```
`sayHello(_:)` 方法带一个单一的字符串参数，然后在前面打印一个 “Hello”，后面跟着函数参数单词 “World”。

现在打开 `main.swift`， 然后替换原来的内容为下面代码：
```swift
if Process.arguments.count != 2 {
    print("Usage: hello NAME")
} else {
    let name = Process.arguments[1]
    sayHello(name)
}
```
跟之前的硬编码不同，`main.swift` 现在从命令行参数中读取。替代之前直接调用 `print(_:)`， `main.swift` 现在调用 `sayHello(_:)` 方法。因为这个方法是 `Hello 模块`的一部分，所以不需要使用到 `import` 语句。

运行 swift build 并尝试 Hello 的新版本：
```swift
$ swift build
$ .build/debug/Hello 'whoami'
```
目前为止，你已经能够运用开源 Swift 来运行一些你想要的程序了 。
要了解Swift包管理器，包括如何构建模块、导入依赖项和映射系统库，请参阅网站的[Swift包管理器](https://swift.org/package-manager)部分。
