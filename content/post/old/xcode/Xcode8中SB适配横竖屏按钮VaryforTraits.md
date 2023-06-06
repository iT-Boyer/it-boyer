---
title: Xcode8中SB适配横竖屏按钮VaryforTraits
date: 2017-06-13 17:33:59
updated: 2019-01-16 21:01:14
comments: true
tags: [Xcode,工具]
categories:
- 学习笔记
keywords: 
permalink: 
---

升级xcode8之后，打开storyboard发现xcode7适配界面的size class被Trait Variations所取代:
Trait Variations只是size class的直观表现方式，改善了原本九宫格选取过于抽象的问题，直接选机型很清晰直观，但本质未变。

### 添加竖屏约束

xcode8版本的选择器，由原来的九宫格形式，改进为机型选择器：

以前版本：

#### Vary for Traits
对不同设备和方向上添加约束
1. 点击右侧的Vary for Traits 会弹出选择Width／Height或组合, 左边的会立即显示将适配的所有机型和方向，即此时新增的约束应用到的不同方向的所有机型   
假如：选width，会发现约束会同时应用到iPhone的横竖屏：
选中了Height之后（这里Width选不选中都是可以的），会发现左侧横屏的设备消失，接下来添加的约束就只会运用到竖屏界面上了  
接下来为竖屏状态的界面添加约束，
3. 点击Done Varying按钮完成对约束的添加   

### iPad适配时无法区分横竖屏
对比iPhone横竖屏：
竖屏状态是wC：hR 横屏下是wC：hC (w是width h是height，C是Compact R是Regular) ，所以可以方便横竖屏俩套UI是由于横竖屏的size class是不同的。   
但iPad横竖屏都是wR：hR, 所以在竖屏设置的约束同样会应用到横屏上。
所以iPad横竖屏布局还得监听屏幕旋转更改约束，或者在layoutSubview中修改frame了。所以就目前而言一套界面同时适配iPhone与iPad横竖屏这4种界面还是有些困难，也很少(没有？)有应用这样干，大多数应用还是单独做了HD版本，或者直接用iPhone的布局方案。
