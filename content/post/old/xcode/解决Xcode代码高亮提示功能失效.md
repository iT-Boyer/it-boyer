---
title: 解决Xcode代码高亮提示功能失效
date: 2018-08-31 16:20:20
updated: 2019-01-16 21:01:14
comments: true
tags: [xcode]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
### 方法一
1. 打开失效的项目，选择菜单：Xcode->File->Project Settings -> Advanced..，
2. 删除图上所示的位置目录，重启xcode。
### 方式二
1. cd进入~/Library/Developer/Xcode/DerivedData
2. 找到你的项目所用的目录（一般以你的项目名开头）
3. cd 目录名
4. rm -r Index 删除掉你的项目所用的索引文件夹
### 方法三
1. 退出 Xcode
2. 重启电脑
3. 找到 这个 DerivedData 文件夹 删除 (路径: ~/Library/Developer/Xcode/DerivedData)
4. 删除这个 com.apple.dt.Xcode 文件 (路径: ~/Library/Caches/com.apple.dt.Xcode)
5. 运行 Xcode  就好了~~
>(1) 原文表示删除 ~/Library/Developer/Xcode/DerivedData下所有的文件，我尝试发现只需要删除当前项目相关的索引文件即可
>(2)  DerivedData从字面上理解应该是收集到的数据，应该是Xcode针对这个项目缓存的一些数据，不会影响项目本身的完整性
