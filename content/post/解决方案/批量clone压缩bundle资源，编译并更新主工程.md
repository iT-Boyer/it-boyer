+++
title = "使用swift-sh写一个脚本批量编译bundle文件功能"
description = "通过swift语法实现shell命令和ruby命令，还有fastlane工具的使用方法的学习"
date = 2021-11-24T15:00:00+08:00
publishDate = 2021-11-24T15:00:00+08:00
lastmod = 2021-12-02T18:54:22+08:00
categories = ["解决方案"]
draft = true
authors = ["iTBoyer"]
+++

在处理瘦包过程，主要针对图片资源文件进行压缩，具体步骤：  

先检索主工程中的bundle资源清单，再根据bundle包名找到git源码库，clone再压缩图片，编译bundle target，更新主工程中的bundle 包，commit+push 结束。  

现在面临的问题：swift基础语法，swift-sh脚本使用，xcodebuild工具获取target方法，拷贝替换方法。其中swift-shell调用shell命令，swiftJSON解析json文件。  

fastlane获取项目名称拼接git路径，下载源码，打印源码目录，压缩图片，提交代码到主线。  

熟练使用swift-sh编写效率脚本，提高swift学以致用的目的。扩展命令行，增加入参交互的技巧，解锁脚本更多的使用场景。  


## 要事第一，制定下一步行动 {#要事第一-制定下一步行动}

swift编写的方法还是借助xcode有点：输入自动补齐和校验功能，swift-sh提高生成Xcode功能文件的方法，可以做到完美编码交互体验  

1.  使用swift-sh 初始化脚本和编写环境
2.  swift 调用shell 脚本使用[SwiftShell](https://kareman.github.io/SwiftShell/index.html)学习。


### <span class="org-todo todo TODO">TODO</span> 待办任务 {#待办任务}

-   [ ] 整理swift 开发的知识点  
    1.  swiftshell
    2.  json 解析
    3.  swift 语法
    4.  swift-sh 工具使用：转swift 文件转为spm 项目

fastlane 支持spm 编辑工具，正符合之前预想的效果，借助SPM 和 fastlane 强大的action,定制自己的工具。 fastlane 使用的是swiftshell, 正好是刚学习的工具。  

1.  定制swift 版本的action
2.  fastlane lane 用法，可以extenion 进行扩展，但是在名下只能看到fastfile 文件下的lane
3.  净化iAlfred 库，把有用的swift 命令，迁移到faslane action 中。
4.  新建fastlane 库，专门支持spm 的工具，很符合命令工具集合的管理。
5.  整理swiftshell 文章。

fastlane 从gem 到swift 开发，再到spm 管理机制的支持，可以在命令行工具中集成fastlane. 目前实践的方式是新建命令行工具Runner, 依赖swiftJSON 和fastlane( 依赖引用本地fastlane库源码，方便自定义public接口) 整合了之前中常用的几个lane 和 actions ，新增了终端脚本bundleMe: 压缩bundle 资源包图片的action 功能：runner lane bundle 支持参数解析巩固脚本编写的流程，培养命令行思维方式解决问题
