---
title: 序列图sequence语法插件
date: 2017-06-24 16:00:03
updated: 2018-05-29 13:58:23
comments: true
tags: [UML]
categories: 
- 学习笔记
keywords: 
permalink: 
---
## MarkDown插件
{% github bubkoo hexo-filter-sequence  4ab9c15  width = 30% %}
这个插件在markdown暂时无法渲染出图像，不建议使用。

## 官方
[序列图预览工具](https://bramp.github.io/js-sequence-diagrams/)


## 概述
序列图(sequence diagram)，又称时序图。

1. 通过描述对象之间发送消息的时间顺序
2. 显示多个对象之间的动态协作
## 元素
=====

```sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```
### 角色Actor
系统角色。
http://blog.csdn.net/ethmery/article/details/50670284
### 标题
MarkDown语法：
```
Title:[标题]
```
### 对象Object
位于序列图顶部，代表参与交互行为的对象。常见的命名方式：
1. 类名+对象名
2. 类名，无对象名（匿名对象）
3. 对象名，无类名
对象(object)是对客观事物的抽象，类(class)是对对象的抽象。
1. 一个对象可以通过发送消息来创建另一个对象
2. 一个对象可以被删除/自我删除，此时以”×”表示
MarkDown语法：
1. 声明一个或多个对象
```
participant [对象名1]
participant [对象名2]
```
```sequence
participant A
participant B
Note left of A: 这是对A的注释
Note right of B: 这是对B的注释
Note over A: 这是A上的注释
Note over A,B: 这是A和B共有的注释
```
2. 为对象添加注释
```
Note left/right to [对象名]: [注释]
Note over [对象名]: [注释]
Note over [多个对象，之间以,隔开]: [注释]
```

### 生命线Lifeline
1. 序列图中的对象在一段时间内的存在
2. 以从对象底部中心延伸出的竖直虚线表示
3. 对象之间的消息传递发生于生命线之间

### 激活期Activation
1. 序列图中的对象执行一项操作的时期
    * 或执行其自身的代码
    * 或等待另一个对象的返回信息
2. 以生命线上相应时间段内窄矩形表示

### 消息Message
1. 用于对对象间通信内容进行建模的类
2. 以垂直于生命线的单方向箭头表示
3. 消息包含内容：
    * 消息名称
    * 消息参数
    * 可能带有条件表达式，以确定是否发送/发送分支
MarkDown语法：
1. 语法格式：
```
[发送对象][箭头符号][接收对象]: [消息]
```
### 效果实现：
```sequence
A->B: ->
B-->C: -->
B->>A: ->>
C-->>A: -->>
```
2. 定义箭头语法：
    -> 实线黑色三角箭头
    –-> 虚线黑色三角箭头
    ->> 实线开放箭头
    –->> 虚线开放箭头
3.  消息显示顺序与代码中消息编写顺序一致

