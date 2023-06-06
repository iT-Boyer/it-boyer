+++
title = "SPM 管理库依赖和发布私库工具"
lastmod = 2020-09-12T23:14:07+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

-   [X] 问题 1:如何支持 iOS 项目到目前为止，它仅支持命令行工具和服务器端 Swift 应用程序  
    [Swift Package Manager for iOS – Guide & New Features | TSH.io](https://tsh.io/blog/swift-package-manager-for-ios-new-features/)  
     无论您是使用第三方依赖关系，开发自己的内部框架，还是只想提取应用程序代码的一部分以使其模块化和有序（或同时包含所有内容），Swift Package Manager 都将非常有用。随着 Xcode 11 的发布，它将成为 Apple IDE 的重要组成部分。现在，SPM 软件包可与所有 Apple 平台一起使用，包括 iOS 系统（到目前为止，它仅支持命令行工具和服务器端 Swift 应用程序）。如果您尚未使用 SPM，现在是尝试一下的最佳时机！

[Adopting Swift Packages in Xcode - WWDC 2019 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2019/408/)  
[Creating Swift Packages - WWDC 2019 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2019/410)  


## 特点 {#特点}

1.  可以看作是独立于 Xcode 的 workspace 的子项目
2.  非常适合重构可重用代码
3.  未版本化
4.  可以发布和共享


## Creating local packages {#creating-local-packages}

spi : 创建库  
spi --type executable:创建可执行文件  
sps：显示库依赖  
spx:生成 Xcode 项目文件  
spf: fetch 依赖库  
spu: 更新依赖库上述命令\`spi\`的区别是\`Package.swift\`不同，Source 中多一个 main.swift 文件。当 source 中存在 main.swift 时，\`spx\`生成的 xcodeproj 中 target 清单，会显示可执行图标。  


## Publishing packages {#publishing-packages}

配置 pkage.swift，发布到 github 上，可以轻松共享源码，类似 cocoapods 库分享机制。  


## Package manifest API {#package-manifest-api}

[Swift.org - Swift Package Manager Manifest API Redesign](https://swift.org/blog/swift-package-manager-manifest-api-redesign/)  


## Editing packages {#editing-packages}


## Open source project {#open-source-project}
