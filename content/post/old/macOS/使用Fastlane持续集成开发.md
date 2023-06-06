---
title: 使用Fastlane持续集成开发
date: 2018-10-24 12:45:30
updated: 2018-10-24 13:45:36
comments: true
tags: [iOS,工具]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
<!--github库卡片-->
{% github it-boyer  width = 30% %}

## 持续集成
在框架开发中，一个优秀的持续集成环境是至关重要的。CI 可以保证潜在的贡献者在有保障的情况下对代码进行修改，减小了框架的维护压力。大部分 CI 环境对于开源项目都是免费的，得益于此，我们可以利用这个星球上最优秀的 CI 来确保我们的代码正常工作。

就 iOS 或者 OSX 开发来说，`Travis CI`, `CircleCI`, `Coveralls`，`Codecov` 等都是很好的选择。

开发总是有趣的，但是发布一般都很无聊。因为发布流程每次都一样，非常机械。无非就是跑测试，打 tag，上传代码，写 release log，更新 podspec 等等。虽然简单，但是费时费力，容易出错。对于这种情景，自动化流程显然是最好的选择。而相比于自己写发布脚本，在 Cocoa 社区我们有更好的工具，那就是 [fastlane](https://fastlane.tools)。

fastlane 是一系列 Cocoa 开发的工具的集合，包括跑测试，打包 app，自动截图，管理 iTunes Connect 等等。

不单单是 app 开发，在框架开发中，我们也可以利用到 fastlane 里很多很方便的命令。

使用 fastlane 做持续发布很简单，建立自己的合适的 Fastfile 文件，然后把你想做什么写进去就好了。比如这里是一个简单的 Fastfile 的例子：
```
# Fastfile
desc "Release new version"
lane :release do |options|
    target_version = options[:version]
    raise "The version is missed." if target_version.nil?
    ensure_git_branch                                             # 确认 master 分支
    ensure_git_status_clean                                       # 确认没有未提交的文件
    scan                                                          # 运行测试

    sync_build_number_to_git                                      # 将 build 号设为 git commit 数
    increment_version_number(version_number: target_version)      # 设置版本号

    version_bump_podspec(path: "Kingfisher.podspec",
               version_number: target_version)                    # 更新 podspec
    git_commit_all(message: "Bump version to #{target_version}")  # 提交版本号修改
    add_git_tag tag: target_version                               # 设置 tag
    push_to_git_remote                                            # 推送到 git 仓库
    pod_push                                                      # 提交到 CocoaPods
end

$ fastlane release version:1.8.4
```
AFNetworking 在 3.0 版本开始加入了 fastlane 做自动集成和发布，可以说把开源项目的 CI 做到了极致。在这里强烈推荐大家有空可以看一看这个项目，除了使用 fastlane 简化流程以外，这个项目里还介绍了一些发布框架时的最佳实践。
