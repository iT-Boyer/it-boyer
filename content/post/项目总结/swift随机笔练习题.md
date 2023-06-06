+++
title = "转笔游戏"
description = "设计转笔游戏界面，用例，和功能实现，分解开发任务和进度跟踪"
date = 2022-02-09T14:05:00+08:00
publishDate = 2022-02-09T14:05:00+08:00
categories = ["项目总结"]
draft = false
authors = ["iTBoyer"]
+++

## 需求描述 {#需求描述}

在我的团队中，同一个人总是自愿起立站立或写下会议笔记。理想情况下，每个人都应该分担这个负担，因此我开始在桌上旋转笔以随机选择某人来完成每个任务。这种方法效果很好，但是我们并不总是有钢笔或桌子。 `需求` ：借用转动的随机笔，来指定谁来执行的任务。这个场景很常见，在多个事情面前，不知道该如何抉择时，可以使用这种随机方式，天注定的缘分来行使抉择权。 

愿景：希望能够激发之前的开发经验，能在这个基础上学好新知识，也能温习之前的旧知识。最终实现在开发思维的转变，以 swiftUI 的开发模式来思考需求的实现。 

了解swiftUI 开发模式，积累swift知识体系 

1.  掌握SwiftUI 原生 API
2.  UIKit 混编
3.  SwiftUI状态属性实现数据通信
4.  Combine 使用，推荐：swiftui-notes
5.  记录在项目中会遇到哪些问题


## 用例描述 {#用例描述}

1.  转笔选择：当点击转笔时，开始旋转，会在随机方向停下来。
2.  团队人数：通过滑块控制笔下静止位置的数量。


## 开发设计 {#开发设计}

产出时序图，类图，活动图，甘特图 


## 难点调研 {#难点调研}

列出项目中，可能用到的技术点，做好调研工作。 

1.  转笔动画
2.  随机人数设置和停止
3.  网络请求获取笔的样式
4.  扩展支持加载远程图片的方法先通过本地配置文件，提供图片的名称、地址等信息，代替服务器端。知识点：文件解析方式，swiftUI 数据层交互方式，网络访问方式，响应式开发。
5.  SPM 学习


### Slider 控件支持绑定State 属性 {#slider-控件支持绑定state-属性}


### Image 图片自适应大小的方法和frame 属性的使用 {#image-图片自适应大小的方法和frame-属性的使用}


### bundle 加载main 和 module 中的资源方法 {#bundle-加载main-和-module-中的资源方法}


### String 格式化小数点的方法 {#string-格式化小数点的方法}


### VStack 布局封装和间距和外边距的设置 {#vstack-布局封装和间距和外边距的设置}


## 开发：要事第一 {#开发-要事第一}

源码库：[Landmarks/Sources/Pen](https://github.com/it-boyer/Landmarks) 


### 转笔页面 {#转笔页面}


### 设置页面 {#设置页面}


### 转笔样式 {#转笔样式}


### 封装自己的网络库 {#封装自己的网络库}


## 回顾总结 {#回顾总结}

`访问taskjuggler版本` [转笔游戏](https://it-boyer.github.io/iDocs/taskjuggler/%E8%BD%AC%E7%AC%94%E6%B8%B8%E6%88%8F) 原文：随机转笔:这是一个解决常见问题的傻瓜应用程序：随机选择一个人来完成任务。 [Swift Package Manager for iOS | raywenderlich.com](https://www.raywenderlich.com/7242045-swift-package-manager-for-ios) 

项目地址：[GitHub - iT-Boyer/PenOfDestiny](https://github.com/iT-Boyer/PenOfDestiny) 

2021-04-07 

2021-04-10 

ox a2
