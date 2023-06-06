+++
title = "总结集成工具使用文档"
date = 2019-09-07T20:47:00+08:00
lastmod = 2019-09-07T20:47:29+08:00
tags = ["文档"]
categories=["学习笔记"]
draft = false
author = "iTBoyer"
#password = 1234
+++

## <span class="org-todo done DONE">DONE</span> jazzy工具使用实现基本的文档的生成,并同步到hugo目录中 {#jazzy工具使用实现基本的文档的生成-并同步到hugo目录中}

支持oc/swift文档

1.  swift文档
2.  oc 文档
3.  .jazzy.yaml配置文件使用

    {{< highlight sh >}}
    author: Alamofire Software Foundation
    author_url: http://alamofire.org/
    github_url: https://github.com/Alamofire/Alamofire
    root_url: https://alamofire.github.io/Alamofire/
    module: Alamofire
    output: docs
    theme: fullwidth
    xcodebuild_arguments: [-workspace, 'Alamofire.xcworkspace', -scheme, 'Alamofire iOS']
    {{< /highlight >}}

    使用test.sh

    {{< highlight sh >}}
    #!/bin/bash
    jazzy --config .custom.jazzy.yaml
    jazzy --config .admin.jazzy.yaml
    jazzy --config .swiftfire.jazzy.yaml
    echo "Jazzy completed"
    {{< /highlight >}}


### <span class="org-todo done DONE">DONE</span> 在Xcode 11 bata5之后,无法显示类结构问题 {#在xcode-11-bata5之后-无法显示类结构问题}

参看:<https://github.com/realm/jazzy/issues/1095#issue-488350044>


## <span class="org-todo done DONE">DONE</span> cocoapods私库的使用,在iSmallAPP中使用私库 {#cocoapods私库的使用-在ismallapp中使用私库}

1.  在ismallAPP的bundle中发布一个私库
2.  总结发布流程,运用到工作中,并发布jazzy文档
3.  使用fastlane发布cocopods私库

    {{< highlight sh >}}
    - ERROR | [iOS] unknown: Encountered an unknown error (Could not find a `ios` simulator (valid values: ). Ensure that Xcode -> Window -> Devices has at least one `ios` simulator listed or otherwise add one.) during validation.
    {{< /highlight >}}

    解决办法: 升级CocoaPods（使用的gem 源： <https://gems.ruby-china.com/>）

<!--listend-->

{{< highlight sh >}}
  sudo gem install cocoapods
  fastlane podPushLane version:1.3 project:LogSwift
或
pod lib lint LogSwift.podspec --allow-warnings
{{< /highlight >}}

错误2

{{< highlight sh >}}
- NOTE  | xcodebuild:  error: SWIFT_VERSION '3.0' is unsupported, supported versions are: 4.0, 4.2, 5.0. (in target 'LogSwift' from project 'Pods')
{{< /highlight >}}

原因:  Usage of the `.swift\_version` file has been deprecated! Please delete the file and use the `swift\_versions` attribute within your podspec instead.
解决: 删除目录下.swift\_version

错误3:

{{< highlight sh >}}
Validating spec
-> LogSwift (1.6)
- ERROR | [iOS] unknown: Encountered an unknown error (Unable to find a specification for `RNCryptor (~> 5.1.0)` depended upon by `LogSwift`

                                                       You have either:
                                                       * out-of-date source repos which you can update with `pod repo update` or with `pod install --repo-update`.
                                                       * mistyped the name or version.
                                                       * not added the source repo that hosts the Podspec to your Podfile.

                                                       Note: as of CocoaPods 1.0, `pod repo update` does not happen on `pod install` by default.
                                                      ) during validation.

[!] The `LogSwift.podspec` specification does not validate.
{{< /highlight >}}

解决方法: pod repo update 更新pod索引库重新发布: pod repo push PodRepo 'LogSwift.podspec' --allow-warnings

问题4

{{< highlight sh >}}
[!] The repo `PodRepo` at `../../../../.cocoapods/repos/PodRepo` is not clean

原因:
git status

.DS_Store
ArcProgressUI/.DS_Store
LogSwift/.DS_Store
MusicLrc/.DS_Store

$ git clean [参数] //-n 显示将要删除的文件和目录 -f 删除文件 -df 删除文件和目录
{{< /highlight >}}

1.  使用fastlane生成jazzy私库


## <span class="org-todo done DONE">DONE</span> fastlane工具使用,编译Xcode工程,并发布到蒲公英 {#fastlane工具使用-编译xcode工程-并发布到蒲公英}

    使用fastlane编译APP生成ipa(暂不支持personal teams)
    >Xcode已经支持个人账号(不是开发者账号)运行在真机,在这种情况下,如何使用
    fastlane编译app并运行到真机上?
[fastlane doesn't support personal Apple IDs for now](<https://github.com/fastlane/fastlane/issues/9041#issuecomment-298867242>)
    上传ipa到蒲公英


## 类似jenkins的工具学习集成的基本操作流程 {#类似jenkins的工具学习集成的基本操作流程}

<span class="timestamp-wrapper"><span class="timestamp">[2019-08-30 Fri 09:55]</span></span>
