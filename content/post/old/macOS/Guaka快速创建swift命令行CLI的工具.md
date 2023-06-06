---
title: Guaka快速创建swift命令行CLI的工具
date: 2018-11-18 19:10:49
updated: 2018-11-18 19:10:49
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
<!--github库卡片-->
{% github nsomar Guaka 7d6135f width = 30% %}
###  Guaka介绍
通过 Guaka Framework 来实现 CLI(command-line interface)。
该框架可以快速实现如下的效果：
```
git checkout -v "url"
```
`git` 就是一个 **CLI**，`checkout`是这个**CLI**的子命令，并接受一个` String` 作为他的参数。

对应上述的命令可以为分为：
* `git` 主命令
* `checkout` 子命令
* `-v/–v` 命令所接收的 flag
```
└── git
    ├── checkout -v
    └── push -f
    └── ...
```
在 Guaka 中代码表现基本就是这样子的：
```swift
let flag = Flag(longName: "v", value: false, description: "Show verbose")
let command = Command(usage: "git", flags: [flag]) { flags, args in
    let showVerbose = flags.getBool(name: "v")
    // args the positional arguments passed to the command
}
```
上面就是 git 的 -v 指令，是否打印过程.

### Guaka快速上手
1. 安装
```
> brew install oarrabi/tap/guaka
```
2. 新建工程
假设我们要建立一个 **papa** 的CLI指令
```
cd 到需要建立工程的目录
guaka create papa
```
会生成如下路径
├── Package.swift
└── Sources
    ├── main.swift
    ├── root.swift
    └── setup.swift 

3. 增加指令
给 **papa** 增加个子命令叫做 check
```
guaka add check
```
增加 flag
增加 flag 就要去 Source 下面对应的 Swift 文件中修改。

4. 编译执行 Swift build
```
swift build
```
// 会编译生成可执行文件
// .build/debug/papa --help

5. 运行**papa**可执行文件
```
.build/debug/papa check
```
增加逻辑就在对应的 Swift 文件中增加.

