---
title: 配置Podfile支持playground导入库
date: 2018-10-06 14:50:12
updated: 2018-10-06 14:50:12
comments: true
tags: [pod,playground]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## pod支持问题
[xcode-playground-with-cocoapods](http://stackoverflow.com/questions/38216238/xcode-playground-with-cocoapods#)
You could use [ThisCouldBeUsButYouPlaying](https://github.com/neonichu/ThisCouldBeUsButYouPlaying) or add this to your Podfile
xcode7.3.1和cocoapods1.0版本导致playground无法import相关动态库
## RxSwift资源案例
在项目中使用`RxSwift.Resources.total`，提供所有Rx资源分配的计数，这对于在开发期间检测泄漏非常有用。
在写入磁盘之前，修改一些工程的配置[post_install hook](https://guides.cocoapods.org/syntax/podfile.html#tab_group_hooks):
```
target 'AppTarget' do
    pod 'RxSwift'
end

post_install do |installer|
    installer.pods_project.targets.each do |target|
        if target.name == 'RxSwift'
                target.build_configurations.each do |config|
                    if config.name == 'Debug'
                    config.build_settings['OTHER_SWIFT_FLAGS'] ||= ['-D', 'TRACE_RESOURCES']
                end
            end
        end
    end
end
```
Run pod update.
Build project (Product → Build).

## post_install 用法
[podfile hook语法](https://guides.cocoapods.org/syntax/podfile.html#tab_group_hooks)
[post_install语法](https://www.jianshu.com/p/9dfd59c7c411)
[把玩CocoaPods post_install 和 pre_install](https://www.jianshu.com/p/d8eb397b835e)
