---
layout: post
title: UIImage的渲染模式
date: 2015-11-26 16:15:11 +0800
comments: true
tags: [API]
categories:
- 解决方案
---

设置UIImage的渲染模式：UIImage.renderingMode
在 iOS 7 中 UIImage 添加了一个 `renderingMode` 属性。我们可以使用 `imageWithRenderingMode:`并传入一个合适的`UIImageRenderingMode` 来指定这个 image 要不要以 Template 的方式进行渲染。
```objc
UIImageRenderingModeAutomatic // 根据图片的使用环境和所处的绘图上下文自动调整渲染模式。
UIImageRenderingModeAlwaysOriginal // 始终绘制图片原始状态，不使用Tint Color。
UIImageRenderingModeAlwaysTemplate // 始终根据Tint Color绘制图片，忽略图片的颜色信息。

UIImage *img = [UIImage imageNamed:@"myimage"];
img = [img imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
//实际效果，效果依旧显示为baritem的Tint Color
UIBarButtonItem *barButtonItem = [[UIBarButtonItem alloc] initWithImage:setImage
                                                   style:UIBarButtonItemStylePlain
                                                  target:self
                                                  action:@selector(setAction:)];
```    
在新的 Xcode 中，我们可以直接在 Image Asset 里的 Render As 选项来指定是不是需要作为 template 使用。相应的，在`UIApperance`中，Apple 也为我们对于 `Size Classes` 添加了相应的方法。使用 `+appearanceForTraitCollection:` 方法，我们就可以针对不同 trait 下的应用的 apperance 进行很简单的设定。

```objc
UIView.appearanceForTraitCollection(UITraitCollection(verticalSizeClass:.Compact)).tintColor = UIColor.redColor()  
UIView.appearanceForTraitCollection(UITraitCollection(verticalSizeClass:.Regular)).tintColor = UIColor.greenColor()  
```
