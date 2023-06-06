---
title: Crash文件分析方法
date: 2017-02-07 12:38:58
updated: 2017-05-26 11:03:05
comments: true
tags: [调试]
categories:
- 解决方案
keywords:
permalink: 
---

第一步：在任意目录创建调试crash的目录

第二步：将之前Archive的文件copy到crash目录里面,其中包括两个文件.app和.app.dSYM

第三步：将symbolicatecrash工具copy到crash目录
```ruby
find /Applications/Xcode.app -name symbolicatecrash -type f
```
2.用命令将symbolicatecrash拷贝到桌面的crash文件夹里面，与.app和.app.dSYM放一起
拷贝到crash目录：
```sh
cp /Applications/Xcode.app/.../symbolicatecrash /Users/Desktop/crash
```
第四步：执行symbolicatecrash
1.打开终端用命令切换到桌面的crash目录下：
```sh
cd /Users/你的电脑名称/Desktop/crash
```
2.执行命令
```sh
./symbolicatecrash /Users/Desktop/crash/PBB.crash /Users/Desktop/crash/Control.app.dSYM > Control_symbol.crash
```

这时候终端有可能会出现：`Error: "DEVELOPER_DIR" is not defined at ./symbolicatecrash line 60.`

3.输入命令：
```sh
export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer" 
```
4.再执行 2.的命令行

5.将终端完成以后，在crash文件夹里面会多出一个文件Control_symbol.crash。
```sh
Unsupported crash log version: 12 at ./symbolicatecrash line 614.
```

第五步：
```sh
dwarfdump --lookup 0x000cf358 --arch armv7 appname.app.dSYM/
```
