---
title: 支持OSX浏览PDF简单视图
date: 2017-09-08 18:48:43
updated: 2018-10-12 19:56:59
comments: true
tags: [pdf]
categories:
- 解决方案
keywords: 
permalink:
---




运行scheme： `PDFReaderForOSX` 即可

{% github it-boyer PDFReader 737a7e %}

## 支持iOS

pageViewController: pdf翻页效果视图控制器
startingViewController:DataViewController,翻页视图控制器的视图源
modelController:ModelController:NSObject,数据视图数据源的model模型。
```puml
@startuml
object 阅读器
object pageView
object source
object model
@enduml
```
