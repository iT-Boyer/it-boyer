---
title: 自定义Xcode支持Vim快捷键
date: 2018-09-20 21:12:51
updated: 2018-09-20 21:12:51
comments: true
tags: [xcode]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
使用了Vim之后，被其强大的文本编辑能力吸引, 于是每天都在寻找 Vim 插件, 试图用 Vim 代替 Xcode 进行日常开发. 直到有一天突然发现, 我想要的就是一个**拥有强大文本编辑能力的 Xcode.**
分享一个[自定义快捷键完整版](https://github.com/Scyano/XcodeKeyBindingSet.git)
## 常用操作
Xcode虽然没有 Vim 的模式切换, 但是也可以使用大部分文本操作, 这里主要用到的是:
1. 删除: 完全删除单行, 删除单行到行首, 删除词组, 删除单词, 删除段落
2. 替换: 替换单行, 替换词组
3. 复制: 复制行尾词组, 复制词组, 复制单行.
另外还有向前删除单行, 向后删除单行, 全局替换, 剪切单行, 格式化粘贴单行等.

## 系统自带快捷操作

首先 Xcode 原生自带的快捷键就非常实用了, `CMD+`, 打开`Preferences`->`Code Binding`->`Text `可以看到常用的选择, 删除, 搜索等快捷操作, 如 `Move Word Right` => `⌥+→` : 光标向右按单词移动.

## 自定义快捷操作
实际使用时发现系统提供的删除只可以删除单词或词组某一方向的字符:
系统快捷键演示：
![](https://upload-images.jianshu.io/upload_images/1253941-d451d9eadbbb6fa5?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)

### 自定义快捷键
所以, 决定自行添加快捷操作, 而 Xcode 本身就是一个OSX 应用程序, 所以我们可以找到相关文件去配置快捷操作。
1. 了解并编辑`IDETextKeyBindingSet.plist`
`Application`->`Xcode.app`->`Contents`->`Frameworks`->`IDEKit.framework`->`Resources`->`IDETextKeyBindingSet.plist`
使用编辑器打开IDETextKeyBindingSet.plist, 自行添加快捷操作:
```
<key>Delete Current Word</key>
<string>moveLeft:, moveWordRight:, deleteWordBackward:</string>
```
自定义快捷键演示
![](https://upload-images.jianshu.io/upload_images/1253941-a04470b8b26e6fe8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)
其中`Cut Current Line` 是显示在 Xcode 偏好设置内的快捷操作.`moveToEndOfLine` 是编辑器的文本操作相关的 `编辑器API`, 可以组合类似的 `API` 自定义操作.
2. 更新到xcode设置快捷键
编辑完成后, 重新打开 `Xcode->Preferences->Key Bindings->Customized`, 为自定义的快捷操作添加快捷键.
![](https://upload-images.jianshu.io/upload_images/1253941-717652603f41af8d?imageMogr2/auto-orient/strip%7CimageView2/2/w/960)
分享一个[自定义快捷键完整版](https://github.com/Scyano/XcodeKeyBindingSet.git)

