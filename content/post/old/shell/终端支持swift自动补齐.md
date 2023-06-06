---
title: 终端支持swift自动补齐
date: 2018-08-21 15:48:24
updated: 2018-08-21 17:46:05
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
### 终端支持swift自动补齐
keith/sourcekittendaemon.vim：这个插件提供了Vim集成SourceKittenDaemon。这意味着你可以在vim中开发swift项目  
需要两步：安装sourcekitten,   
#### 第一步安装sourcekitten
1. 安装  
方式一：`brew install sourcekitten`  
方式二：clone源码 ,运行`swift build`   
方式三：clone 源码，运行 `make install`   
方式三：[pkg安装包](https://github.com/jpsim/SourceKitten/releases)  
2. 执行`sourcekitten help`验证安装成功。  

#### 第二步安装:SourceKittenDaemon
1. 安装SourceKittenDaemon环境  
安装并设置[SourceKittenDaemon](https://github.com/terhechte/SourceKittenDaemon)  
方式一：[pkg安装包](https://github.com/terhechte/SourceKittenDaemon/releases/)  
方式二: 1. Clone the repository   2. 安装 `make install`  
2. 执行`SourceKittenDaemon help`验证安装成功。   

### 使用
启动后台驻守服务HTTP：[参考Protocol.org](https://github.com/terhechte/SourceKittenDaemon/blob/master/Protocol.org)   
```
SourceKittenDaemon start --port 44876 --project /private/tmp/abcde/abcde.xcodeproj
```
`--port`: 服务使用的端口号，默认为`8081`，vim目前不支持指定SourceKittenDaemon端口，使用默认的8081。
`--project=`: 指定服务将要加载的`.xcodeproj`文件路径，不支持指定`.xcworkspaces`文件路径

使用get方法请求后驻服务：   
```
/complete  # X-Offset|X-Path|X-File
/stop     # 停止后驻服务. 一般用于为其他target提供服务时，重新启动服务。
/ping     # ping后驻服务，运行正常 return OK
/project  # 打印当前加载的project文件路径。
/files    # 打印一个当前加载的project中包含的所有swift文件列表
```
