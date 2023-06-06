---
title: 将Playground作为项目的单元测试
date: 2018-10-22 16:52:33
updated: 2018-10-22 18:39:30
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

在`Perfect`项目中增加`playground`：
### 新建`Perfect.xcodeproj`和`PerfectPlayground`
1. 新建一个Xcode工程`Perfect`
```
swift package generate-xcodeproj
```
2. 设置include路径
在Xcode工程的`build settings`中设置`SWIFT_INCLUDE_PATH` 路径为 `${PROJECT_DIR}`并设置`recursive`（递归）选项。
3. 在同一工程目录下创建一个`PerfectPlayground`。

### 新建`Perfect.workspace`
1. 新建一个工作空间：`Perfect.workspace`,在工作空间中添加工程和操场：`Perfect.xcodeproj`和`PerfectPlayground`。
2. 编译`Perfect.xcodeproj`，这样就激活了`PerfectPlayground`的`PerfectLib`函数库功能。
```swift
import PerfectLib
let json = "{\"name\": \"tom\"}"
do {
    let name = try json.jsonDecode()
    print(name)
}catch{

}
let u = UUID()
print(u.string)
```

[原文Perfect-Playground](https://github.com/PerfectExamples/Perfect-Playground/blob/master/README.zh_CN.md)
