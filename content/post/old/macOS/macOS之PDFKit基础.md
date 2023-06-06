---
title: macOS之PDFKit基础
date: 2017-02-14 14:29:18
updated: 2019-01-16 21:01:14
comments: true
tags: [PDF]
categories:
- 学习笔记
keywords: 阅读器,macOS
permalink: 
---
一个PDF的基本构建块是Documents本身。Documents通常作为文件存储在磁盘上。
作为文件版本，可以支持元数据标记如作者，创建日期，等等。
一个文件可以加密，需要密码才能查看它。两级加密存在：
* 用户级加密：如果用户成功地获得用户级权限，他或她可以查看文档，但可以限制打印或复制文档。
* 所有者级别加密：获得所有者级别权限的用户可以查看文档并具有完全使用权限。
许多加密的PDF文件有一个“dummy”的用户密码为`空字符串`。大多数PDF文档解析器（包括PDF套件）自动尝试空字符串密码加密后的文件，如果成功，只显示文档。因此，在技术上加密的文档不一定提示用户口令。

## PDF页面
一个PDF文档由若干页面组成。这个页面看起来就像一本物理书页面显示在屏幕上。同时PDF页面可以包含`超链接`和`注释`。页面可以支持`裁剪`，还有其他使用功能：例如隐藏多余的部分（如注册标记）。
### view VS page空间坐标
页面上的大多数对象都是在`page`空间中指定的，而不是在`view`空间中。
也就是说，坐标系统是在点（每英寸72点），`坐标原点`在page左侧底部，而不是`view`。`page`空间不关心缩放，显示模式等等。一个有`bounds`的item，比如说32points，保留这些界限，无论显示大小。
图view和page坐标系比较

`PDFView class`包含几个转换方法，将坐标系统从`view space`的`page space`，反之亦然。

## PDF Kit Classes
`PDF Kit`套件提供了几个不同功能的`类`。
`PDFView`和`PDFSelection`除外，这些`类`大致对应着各个`对象`在PDF格式的规范需求。

### PDFView Class
`PDFView类`，就好比Web工具包的`WebView类`，源于`Application Kit`中的`NSView类`。在项目开发中，你可以使用`Interface Builder`轻松拖动一个`PDFView对象`放在一个window中。~~从/Developer/Extras/Palettes/PDFKit.palette得到调色板。~~
`PDFView`可能是`PDF Kit`中唯一个需要你自定义的的类。在APP中显示PDF数据，允许用户选择文档内容和导航浏览PDF文档，设置缩放级别，复制文本内容到剪贴板。用户可以拖放PDF文档到`PDFView`。
`PDFView`能通过调用其他`PDF实用类`来实现其大部分功能。如果要添加特殊功能，则需要用户自定义`实用类`的子类来扩展其特殊功能。
Utility classes as used by PDFView

### PDF Kit Utility Classes
PDF套件工具类提供一种混合的`Foundation-like`和`Application Kit-like`的行为。他们有类似的`NSString类`和`NSString Additions`方法。这些类都系橙自`NSObject`


#### PDF Document
`PDFDocument`是`PDF kit工具类`中重要类，代表着PDF data或PDF文件。其他实用工具类一般都在`PDFDocument`方法中的实例化。是`PDFPage`和`PDFOutline`；或相关支持操作：`PDFSelection`和`PDFDestination`。
你`PDFDocument对象`初始化，需要一个`PDF数据`或一个指向PDF文件的`URL`。实例化之后就可以访问`页数`，`添加`或`删除`页面，对所选内容为`NSString对象`进行`查找`或`分析`。

#### PDFPage
`PDFPage`代表一个PDF文档的页面。你的应用程序获取一个`PDFPage`对象必须通过从`PDFDocument`对象来实例化。`PDFPage`对象是用户所看到的屏幕，和一个`view`可以同时显示多个`page`。你可以使用`PDFPage`把PDF文档内容渲染到屏幕上，添加`注释`，`计数字符串`，定义`选择`，获取一个`page`中的文本内容作为`NSString对象`或`NSAttributedString对象`。

#### PDFOutline
除了显示实际的文件内容，`PDF Kit`也能呈现`PDFOutline`信息，前提是PDF文档中存在目录结构。在目录结构中，一个`PDFOutline对象`代表一个`父目录`或`子目录`。
目录是由一个层次的`PDFOutline对象`组层。顶层是`根目录对象`，它仅作为其他目录对象的容器。用户的`根目录`是不可见的。

#### PDFSelection
一个`PDFSelection`对象包含一个跨PDF文档中文本。你不要直接创建`PDFSelection`。`PDFSelection`对象是作为返回值来实例化的。例如：通过调用`PDFPage`或`PDFDocument`对象中的selection方法，并从成功搜索的返回值来实例化`PDFSelection`对象。
在同时显示多个`PDFPage`的`PDFView`中，`PDFSelection`可以是不连续的，或两者兼具的。例如，可以选择在一个单柱连续两列页面的文本。可以在任何方向从一个选择区, 合并选择区, 或扩展选择区中得到文本和网页覆盖。

#### PDFAnnotation
一个`PDFAnnotation`对象可以表示多种内容以外，在一个PDF文件的主要文本内容：链接、表单元素，突出圈子，等等。每个注释与页面上的特定位置相关，并且可以与用户提供交互性。
`PDFAnnotation`是如图所示的具体类的抽象类。各种具体类代表`PDF Kit`支持的注释类型。


#### PDFBorder
`PDFBorder`对象封装的拉伸`PDFAnnotation`对象边框的行为。可以定义一个PDF的bord线的风格属性（例如，固体，破灭，或斜面），bord线的宽度，和圆角半径。


过期:~~从/Developer/Extras/Palettes/PDFKit.palette得到调色板。~~
Here's what Apple says about it:
{% blockquote 老文档  http://developer.apple.com/library/mac/#documentation/GraphicsImaging/Conceptual/PDFKitGuide/PDFKit_Prog_Tasks/PDFKit_Prog_Tasks.html PDFKit_Prog_Tasks %}
"To add the PDFKit palette in Interface Builder, select the Palettes tab in the Preferences panel. Click the Add
button, navigate to the /Developer/Extras/Palettes folder, and select the PDFKit palette. Next, select the Customize Toolbar menu item in the Tools/Palettes menu and drag the PDFKit palette to the toolbar to make it visible."
{% endblockquote %}
So:
there's no Palettes tab anywhere in the Preferences panel and the Xcode Help return a blank when searching for it.
There's no PDFKit.palette file anywhere on my HD (says Spotlight).
I guess these instructions are for an older version of XCode but it's all Apple seemed to have on it...
