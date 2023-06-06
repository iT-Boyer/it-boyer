---
title: Xcode10新特性
date: 2018-10-02 13:18:21
updated: 2018-10-03 16:55:41
comments: true
tags: []
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
Xcode 10
Xcode 10在macOS Mojave的黑暗模式下看起来非常棒，也让你很容易在macOS应用程序中采用新的外观。Xcode 10测试版包括Swift 4.2和beta sdk，适用于iOS 12、watchOS 5、tvOS 12和macOS Mojave。
## Dark模式界面和Mac程序支持
* 全新的`dark`外观贯穿`Xcode`和`Instruments`
在终端中输入命令，Xcode10开启dark mode:
```
defaults write com.apple.dt.Xcode NSWindowDarkChocolate -bool true
```
    使用低版本的macOS,使用此命令可能会造成Xcode interface损坏(测试系统 macOS High Sierra
    10.13.4),只能使用macOS Mojave 10.14+才能使用暗黑模式
    
* `Asset catalogs`为自定义`colors assets` 和 `image assets` 添加了深色和浅色变体，支持不同的图像与颜色assets的亮暗与高对比度表现。
支持CarPlay assets.
支持ARKit 3D ARReferenceObject assets.
Asset目录与视图调试器(view debugger)的背景可以被明确设定成亮或暗，这样前景的元素就会明显看出来。
* `Interface Builder`支持`dark`和`light`预览之间切换
调试您的Mac应用程序在黑暗或光明的变种，而不改变OS设置
## Source Control
1. 在共享服务器上的本地存储库或上游的更改直接在编辑器中突出显示。乍一看，你会发现:
修改代码。
    * 更改尚未推入共享存储库。
    * 其他人已经做出了上游的改变。源编辑器(source editor)会显示出某个开发者做出的改变并展示其他开发者做出的还未添加到项目中的改变。
    * 在承诺之前，你应该解决冲突。
支持Atlassian Bitbucket提供的云托管和自托管Git服务器，以及与现有GitHub支持一起使用的GitLab。
Xcode提供了在从存储库中提取最新版本的代码时重新设置更改的基础。
如果需要，将生成SSH密钥，并将其上载到服务提供者。
## 编辑器的改进
* 在代码编辑器中放置多个游标，以便同时进行许多更改。
Xcode 10 Source Editor现在支持多光标编辑，允许你快速同时编辑多范围的代码。你可以通过多种方式放置额外的光标，包括鼠标点击方式`⌃+⇧+Click`，或通过选择列`⌥+Click+Drag`，或通过键盘的`⌃+⇧+Up`选择上一列，或`⌃+⇧+Down`选择下一列。
* 代码折叠带现在可以隐藏任何被大括号包围的代码块。
* Over-scroll功能可以方便地将屏幕中间的最后几行代码居中。
* 库(Library)内容从Inspector区的底部移动到了一个重叠窗口中，这个窗口可以移动或调整大小，就像Spotlight search一样。在拖动物品时它会解除，但在拖动前按住Option键，它就会为一个额外的拖动保持开启。
## Playgrounds支持机器学习
* `REPL-like` model 立即重新运行您现有的playground代码。
* 代码运行到指定的行，或键入`shift-return`来运行刚刚添加的代码。
* import[Create ML  Framework](https://developer.apple.com/machine-learning/build-a-model/)以交互式地培训新模型，然后在playground上编写代码的测试模型。完成后，将模型拖放到应用程序中。
## 测试和调试
* `Debugging symbols`从新设备下载的速度比以前快了五倍。
* Xcode将生成一组完全相同的模拟器，以充分利用多核Mac的优势，并通过风扇测试并行运行，以更快的速度完成测试套件。
* 以随机或线性顺序运行测试。
* Instruments工具会自动显示添加到代码中的OSLog路标。
* 构建并共享您自己的定制`instruments package`，为您自己的代码提供独特的数据可视化和分析。
* `Memory`内存调试器使用紧凑的布局，以便更容易地研究内存图。
* `Metal shader`调试器允许您轻松地检查顶点、片段、计算和平铺着色器代码的执行。
* `Metal dependency viewer`可以预览在你的`Metal-based`应用程序中如何使用资源的详细图表。
* Xcode的视图调试器添加了一个选项，可以选择亮暗canvas背景色。
你可以改变你macOS应用运行时的外观，通过使用`Debug > View Debugging > Appearance menu`，或调试栏中的`Override Appearance`菜单，或接触栏(touch bar)
检查器(inspector)中展示的已命名颜色在调试时会显示它们的名字以及它们是否为系统颜色。
## 构建性能
* 默认情况下启用`New build system`，改进了整个系统的性能。
Xcode 10使用了一个新的创建系统。新创建系统提供了更好的可靠性与创建性能，它可以获取项目配置问题，而legacy创建系统则不能。
Legacy创建系统在Xcode 10中依旧可用。要想使用的话，在File > Project/Workspace Settings页选择它。为legacy创建系统配置过的项目会在Activity View显示一个橘黄锤子标签

* Swift编译器构建每个单独的文件要快得多。
* 使用`incremental build setting`增量构建设置时，用于调试的大型Swift项目的构建速度要快得多。
[《WWDC》Xcode 10也有暗黑模式，現在整合GitLab程式碼託管服務](https://www.ithome.com.tw/news/123646)
[Xcode10新功能新内容（Beta版下载链接）](https://blog.csdn.net/u010960265/article/details/80630118)
