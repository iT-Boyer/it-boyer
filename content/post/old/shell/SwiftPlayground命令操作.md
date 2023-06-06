---
title: SwiftPlayground命令操作
date: 2018-08-10 15:50:21
updated: 2018-09-29 15:26:30
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github jerrymarino SwiftPlayground.vim f603700 width = 30% %}
## 执行环境
在playgrounds后缀的目录中执行swift文件

## 熟悉vim快捷键用法
空格 + fs 保存文件：此时插件会自动运行playgrounds，显示效果
shift + H ：行头
shift + L：行尾
:copen : AsyncRun显示运行日志
:AsyncRun  shell命令

### 支持结构
`*.playgroud/Contents.swift`
不支持包含：Pages页面的playground。
