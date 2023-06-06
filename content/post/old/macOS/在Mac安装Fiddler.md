---
title: 在Mac安装Fiddler
date: 2018-05-31 16:38:52
updated: 2018-09-22 21:18:58
comments: true
tags: [工具]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
### 安装Mono
Mac下需要使用.Net编译后的程序，首先需要用到跨平台的方案`Mono`(现阶段微软已推出跨平台的方案.Net Core，不过暂时只支持控制台程序)。
[下载地址](http://www.mono-project.com/download/stable/#download-mac)
### 配置Mono环境
1. 下载证书
从Mozilla LXR上下载所有受信任的root证书，存于Mono的证书库里。root证书能用于请求https地址：
```
$cd /Library/Frameworks/Mono.framework/Versions/<mono version>/bin/
$./mozroots --import --sync
```
> ./mozroots命令失效，./cert-sync新命令，暂时不知道怎么使用
2. 配置Mono环境变量

在`.bash_profile`中加入如下内容
```
export MONO_HOME=/Library/Frameworks/Mono.framework/Versions/5.0.1
export PATH=$PATH:$MONO_HOME/bin
```
## Fiddler
[官方文档](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/InstallFiddler)
### 安装
下载[Fiddler-mac.zip](https://www.telerik.com/download/fiddler)压缩包，解压到非中文字符的路径下。
### 运行
打开Terminal，进入到刚才解压的Fiddler路径，执行命令运行：
```
sudo mono Fiddler.exe
```
[参看](http://www.cocoachina.com/apple/20170704/19729.html)
