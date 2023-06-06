---
title: 使用CocoaPods开发静态库
date: 2018-06-21 21:37:16
updated: 2018-10-04 23:48:07
comments: true
tags: [pod]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
Cocoapods作为OS X和iOS开发平台的类库管理工具，已经非常完善和强大。通常我们用pod来管理第三方开源类库，但我们也极有可能会开发一个用pod管理依赖关系的静态类库给其他人使用，而又不愿意公开源代码，比如一些SDK，那么就需要打包成.a文件。本文将以一个依赖于ASIHTTPRequest的静态类库，来演示如何创建使用了CocoaPods的静态类库以及打包的过程。

## 开发静态库（Static Library）

### 搭建pod静态库项目
#### 不基于pod手动创建(deprecated)
1. 在Xcode中创建一个Cocoa Touch Static Library；
2. 创建Podfile文件；
3. 执行pod install完成整个项目的搭建；
4. 如果需要demo，手动创建示例程序，使用pod添加对私有静态库的依赖，重复执行pod install完成示例项目的搭建。

#### 基于pod自动创建
只需要输入`pod lib`命令即可完成初始项目的搭建，下面详细说明具体步骤，以`JHLib`作为项目名演示。
1.执行命令`pod lib create JHLib`。在此期间需要确认下面4个问题。

## 打包类库
需要使用一个cocoapods的插件`cocoapods-packager`来完成类库的打包。当然也可以手动编译打包，但是过程会相当繁琐。

1. 安装打包插件
终端执行以下命令
```sh
sudo gem install cocoapods-packager
```
2. 打包
命令很简单，执行
```sh
pod package BZLib.podspec --library --force
```
其中`--library`指定打包成.a文件，如果不带上将会打包成.framework文件。`--force`是指强制覆盖。最终的目录结构如下
```
|____BZLib.podspec
|____ios
| |____libBZLib.a
```
需要特别强调的是，该插件通过对引用的三方库进行重命名很好的解决了类库命名冲突的问题。
比如你的类库使用了ASI，然后打包成静态库.a文件。外部调用的项目也使用了ASI，那么不会造成冲突。因为在打包的时候，你的类库里的ASI被重命名为项目+ASI的前缀。

[如何打造一个让人愉快的框架](https://onevcat.com/2016/01/create-framework/)
[使用CocoaPods开发并打包静态库](http://www.cnblogs.com/brycezhang/p/4117180.html)
[iOS动态库,静态库以及framework](https://www.jianshu.com/p/2ea267bf0363)
