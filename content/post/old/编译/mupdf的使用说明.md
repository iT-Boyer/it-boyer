---
title: mupdf使用说明
date: 2017-06-19 18:25:32
updated: 2017-06-19 18:25:32
comments: true
tags: [PDF]
categories: 
- 解决方案
keywords: 
permalink: 
---

### 需求描述
目前手机端使用mupdf，通过在底层添加加密方法实现定制pbb阅读器功能。
资源获取如下：
{% blockquote     %}
Download packages of the latest release for your system:
Source code for all platforms.
Windows viewer and tools.
Android viewer on Google Play.
Android viewer APK installer files.
iPad and iPhone version on the App Store.
The latest development source is available directly from the git repository:
git clone --recursive git://git.ghostscript.com/mupdf.git
{% endblockquote %}

总结：没有适配osx版本，放弃。

### 使用apple 官方提供的demo，来分析pdf结构，显示pdf内容。
猜想：这样以来，使用苹果提供相关API，将无法定制底层操作，即无法实现密文浏览功能，故先从明文阅读器开发开始。


### mupdf源码库集成加密
最新整合集成加密到源码库，并发布到git服务器上，便于协作维护:https://server.local/git/mupdf.git
协作过程：
在服务器端添加账号：test  test123
```ruby
$ git clone https://server.local/git/mupdf.git mupdf
$ cd mupdf/thirdparty/
$ git submodule init
$ git submodule update
$ open mupdf/platform/ios/MuPDF.xcodeproj
$ build & run
```
封装MuPDFFramework便于集成至PBBReader中：
初始化mupdf页面接口：
```objc
MuPDFViewController *bindingPhone = [[MuPDFViewController alloc] initWithNibName:nil bundle:nil];
bindingPhone.filename=filename;
bindingPhone.openfilepath=select_files;
[self.view.window.contentViewController presentViewControllerAsSheet:bindingPhone];
```
