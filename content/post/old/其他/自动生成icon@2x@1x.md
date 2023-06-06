---
title: 自动生成icon@2x@1x
date: 2018-10-15 15:21:53
updated: 2018-10-15 17:22:37
comments: true
tags: [工具]
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
{% github rickytan RTImageAssets cf0a641 width = 30% %}

## 自动生成所有所需的应用程序图标
https://github.com/rickytan/RTImageAssets
[iOS开发工具：自动生成@2x,@3x图片](http://blog.yuzhuohui.info/2015/04/25/iOS-image-auto-scale-folder-action/index.html)

## IconMaker程序生成icon
{% gist 816b7ef296219d14edac %} 
 通过原始1024X1024图片来生成各种iphone ios icon，包含Content.json
 
 `IconMaker`使用说明：
1. 默认生成到当前目录:   
```
$ IconMaker 1024.png   #默认放在当前目录
$ IconMaker 1024.png ~/Desktop/icon/  #生成在指定目录icon中
```
2. 生成结果自动结构如下：
|---Images.xcassets/
|------AppIcon.appiconset/
|---------Content.json
|------------con-60@3x.png
3. 拖入xcode即可
