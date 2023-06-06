---
layout: post
title: Realm数据库引擎-swift
date: 2015-12-16 05:56:15 +0800
comments: true
tags: [数据库]
categories:
- 学习笔记
---
中心思想：
继承RLMObject新建数据模型，添加相应属性，即可使用realm数据库引擎，对用户数据创建持久化，更新，删除等操作。
#### 简介：
[Realm](http://realm.io/) 是一个跨平台的移动数据库引擎，于 2014 年 7 月发布，准确来说，它是专门为移动应用所设计的数据持久化解决方案之一。
* 特点：它拥有自己的数据库存储引擎  
    Realm 并不是对 Core Data 的简单封装，相反地， Realm 并不是基于 Core Data ，也不是基于 SQLite 所构建的。它拥有自己的数据库存储引擎，可以高效且快速地完成数据库的构建操作。
* Realm 支持 Swift 、 Objective-C 以及 Java 语言来编写（ Android 平台和 iOS 平台使用不同的 SDK ）。
* Realm 比使用 SQLite 以及 Core Data 要快很多。
* 可视化工具[Realm Browser](https://itunes.apple.com/cn/app/realm-browser/id1007457278?mt=12) ：可以让您轻松地读写 Realm 数据库的逻辑结构以及其中的数据（以 .realm 结尾），虽然 Realm Browser 的功能还十分简陋，真的只能读写而已。
* RealmPlugin：是 Xcode 建模插件，通过 [Alcatraz](http://alcatraz.io/)安装“RealmPlugin”
#### 目的：
学习如何导入 Realm 框架、创建数据模型，实现 Swift 执行查询以及插入、更新和删除记录，以及使用既有的数据库。
#### 实例学习：
测试APP[物种监测](https://github.com/SemperIdem/SISpeciesNotes/tree/master)
用于记录这个 “ 动植物王国 ” 当中所发现物种的相关信息，包括种群数量、发现区域、年龄结构等等。

在 Xcode 当中打开我们的起始项目。此时， [MapKit](http://www.raywenderlich.com/81615/www.raywenderlich.com/21365/introduction-to-mapkit-in-ios-6-tutorial)
已经在项目当中建立好了，而且项目已经拥有了一些简单的创建、更新和删除物种信息的功能.

#### cocoapods安装（Swift 2.1）：
在项目中创建Podfile配置文件，添加RealmSwift支持
```
use_frameworks!   //
pod 'RealmSwift'
```
执行 `pod install`下载realmSwift框架，生成**.xcworkspace**文件，自动重启原始项目。
设置Xcode项目在git版本控制中的忽略配置：详见[.gitignore](http://stackoverflow.com/questions/49478/git-ignore-file-for-xcode-projects)

[use_frameworks!](http://blog.csdn.net/remote_roamer/article/details/47835347)   

    如果在cocoapods 里面不使用 use_frameworks!,则是通过static libraries 这个方式来管理pod的代码。这样就需要在app-Bridging-Header.h 文件里面去import相应的.h 文件。而如果使用了use_frameworks!,则cocoapods 使用了frameworks 来取代static libraries 方式。 

#### 开始使用：
编译并运行这个应用，然后尝试定位到某个您感兴趣的位置（使用模拟器的位置模拟），然后点击右上角的 “+” 按钮创建一个新的标记点。点选地图上的这个标记点，然后点击其弹出来的气泡，接下来会弹出这个标记点的详细信息。随后，点击类别文本框，就可以看到如下图所示的类别列表了:
![](http://cc.cocimg.com/api/uploads/20150505/1430807925718367.jpg)
1. 使用Realm数据库将类别列表持久化
   * 打开**CategoriesTableViewController.swift **文件添加方法：
   ```swift
    private func populateDefaultCategories() {
    self.results = CategoryModel.allObjects() // 1 查询数据返回包含类别对象的RLMResults数组
    if results.count == 0 { // 2   通过返回结果的个数，初始化本地realm数据库
    let realm = RLMRealm.defaultRealm() // 3 访问默认的 realm 单例对象
    realm.beginWriteTransaction() // 4   在默认 realm 数据库中启动一个事务
    let defaultCategories = Categories.allValues // 5 使用Categories 枚举来创建一个含有全部默认类别的数组
    for category in defaultCategories {
    // 6 初始化类别实例对象，设置其 name 属性，加入realm中
    let newCategory = CategoryModel()
    newCategory.name = category
    realm.addObject(newCategory)
    }
    realm.commitWriteTransaction() // 7    调用 commitWriteTransaction() 方法来关闭事务，并且向数据库提交数据
    self.results = CategoryModel.allObjects()
    }
    }
   ```
    在 viewDidLoad() 方法的底部加入以下代码：
    ```swift
    populateDefaultCategories()
    ```
