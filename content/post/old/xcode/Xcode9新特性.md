---
title: Xcode9新特性
date: 2017-06-15 16:54:38
updated: 2019-01-16 21:01:14
comments: true
tags: []
categories:
- 学习笔记
keywords: 
permalink: 
---
## 无线调试
### 升级设备系统
体验iOS11系统需要几步操作：
1. [下载配置文件：iOS_11_beta_Configuration_Profile](https://profile.apple.xsico.cn)，在手机端使用safari浏览器打开链接，自动安装到描述文件中。
2. 设置中的“软件更新”会自动检测到iOS11 Developer beta版本，表明可以下载更新了。

### 配置设备信息
1. 链接你的设备
选择Window -> Device and Simulators，完成之后左侧Connected区域你的设备右侧会显示小地图的图标，表示你已经连接上，此时断开数据线，就可以开始无线调试了。如下图所示：
2. 如果iphone和mac不在同一局域网，你可以按照下图进行设置：
![](https://static.oschina.net/uploads/space/2017/0610/112915_7tCQ_2279344.png)

## xcode 的新特性
1.  集成github
    1. 在偏好设置中，新增github账号
        2. 在导航栏中，新增git版本库导航器
    快速查看本地版本的Branches／Tags和commit时间轴，以及Remotes远程版本库信息。支持版本库基本操作：新建／合并／切换分支，打tag标签。还支持新建远程仓库，删除远程分支。
        通过双击commit的时间轴的一个条目来查看某次提交中文件的更改详情
        3. xcode欢迎页面，clone已有库的界面，可以直接查看readme.md
        
###  markdown的支持
在Markdown文件中，您键入时，标题，粗体和斜体文本，链接和其他格式将立即在编辑器中呈现。Jump Bar甚至可以了解Markdown结构，因此您可以快速浏览README.md和文档文件。
### 色彩管理
在Xcode中的xcassets中添加自定义的颜色，指定颜色名字:`MyColor`，这样就可以在代码和IB中方便的引用了。
1. 右键选择添加New Color Set
2. 点击Any，在右侧区域中轻松设置你的颜色
3. 使用
    代码中引用：UIColor(named:) 新方法引用你的颜色
```swift
view.backgroundColor = UIColor(named:"MyColor")
```
    IB中引用你的颜色
    ![](https://static.oschina.net/uploads/space/2017/0610/113750_bVTy_2279344.png)
