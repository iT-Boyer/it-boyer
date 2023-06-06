---
title: Jazzy生成Swift文档工具
date: 2018-09-05 15:52:33
updated: 2018-10-20 10:48:04
comments: true
tags: [工具]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---

<!--github库卡片-->
{% github realm jazzy 6932e5e width = 30% %}

demo
{% github appcoda SwiftDocSample 24c1649 width = 30% %}

## 使用 Jazzy 产生代码文档

Jazzy 是一款可以为 Swift 和 Objective-C 代码产生具有 Apple 风格的代码文档工具。事实上，Jazzy 会为你创建一个链接所有代码文档的独立网页。它是一款命令行工具，但还是很容易使用的。
[在 Xcode 中使用 Markdown 生成 Swift 代码文档](https://www.jianshu.com/p/7847a5dc52e2)
### 安装
```
$ sudo gem install jazzy
```
gem source不稳定常会导致无法找到jazzy
```
$ gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
$ gem sources -l
https://gems.ruby-china.com
# 确保只有 gems.ruby-china.com
```
腾讯云：` https://gems.ruby-china.com/`
淘宝：`https://ruby.taobao.org/`

### 使用
使用 cd 命令将目录切换到工程对应的目录：
```
cd path_to_project_folder
Jazzy 
```
输入 `Jazzy` 之后敲回车,会将标注为 `public` 的结构写入代码文档。
如果你想要包含所有的实体，就输入一下：
```
jazzy --min-acl internal
```
默认输出的文件夹位于工程的根目录（你也可以更改输出路径），叫 `docs`。
当jazzy不支持swift版本时，会导致错误：
```
Could not parse compiler arguments from `xcodebuild` output.
Please confirm that `xcodebuild` is building a Swift module.
```
这是就需要在`build settings`中设置`swift Language Version`版本号。
### 生成指定的swift版本
如果不是 Swift 的最新版本，发现使用 Jazzy 后没有效果的话，需要先指定 Xcode 支持的 Swift 版本：
```
jazzy --swift-version 2.1.1 --min-acl internal
```
## 帮助
`jazzy -help`查看使用 Jazzy 时可能使用到的参数。当然，你完全可以根据自己的喜好来得到最终的结果。

### 集成Xcode
在项目中添加运行脚本，或创建 其他-->`Aggregate`在`build phases`下添加`New run script phase`

```
author="iTBoyer";
author_url="http://it-boyer.github.io";
github_url="https://github.com/it-boyer";
sdk="iphone";
output="docs/swift_output";   #生成的文档地址
docSetDir="/Users/admin/Library/Developer/Shared/Documentation/DocSets/"

jazzy \
--objc \
--clean \
--author "${author}"\
--author_url "${author_url}" \
--github_url "${github_url}" \
--module-version 0.96.2 \
--xcodebuild-arguments -scheme,"${PROJECT_NAME}" \
--module "${PROJECT_NAME}" \
--sdk  "${sdk}" \
--output "${output}" \
#拷贝到xcode文档目录中
echo "${output}/docsets/${PROJECT_NAME}.docset"
pwd
cp -r "${output}/docsets/${PROJECT_NAME}.docset" "${docSetDir}"
```
安装到Xcode，在编写代码时，可以快速查看帮助。
```
#拷贝到xcode文档目录中
echo "${output}/docsets/${PROJECT_NAME}.docset"
pwd
cp -r "${output}/docsets/${PROJECT_NAME}.docset" "${docSetDir}"
```
## 工具推荐
- [appledoc](https://github.com/tomaz/appledoc)工具install 方法和更新方法相同

```ruby
git clone git://github.com/tomaz/appledoc.git
cd ./appledoc
sudo sh install-appledoc.sh
```
- [dash插件](https://github.com/omz/Dash-Plugin-for-Xcode#readme)的使用
- [下载](https://kapeli.com/dash)
