---
title: 使用screen在后台运行进程
date: 2018-10-21 22:42:59
updated: 2018-10-21 22:42:59
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
## screen
1. 支持会话恢复
    当我们开启screen后，只要screen进程没有终止，其内部运行的会话都可以恢复。网络连接中断临时，用户也可以进入开启的screen中，对中断的会话的进行控制(恢复或删除)。 
通常的用法是在暂时离开的时候，让在screen中运行的会话保持正常运行并将Screen切换到后台。
2. 支持多窗口
    当使用screen时，可以开启多个窗口，让每个会话都独立运行到不同的窗口，并拥有各自的编号、名称等。用户可以通过这些编号及名称进入不同的窗口。
3. 会话共享
    Screen可以让一个或多个用户从不同终端登录一个会话，并共享会话。使用户登陆同一会话的用户看到同一会话内容。 
    同时它可以提供窗口访问权限的设置，对窗口进行密码保护。
#### screen参数
```
-A 　          将所有的视窗都调整为目前终端机的大小
-d  　         将指定的screen作业离线
-h  　         指定视窗的缓冲区行数
-m             即使目前已在作业中的screen作业，仍强制建立新的screen作业
-r             恢复离线的screen作业
-R 　          先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业
-s 　          指定建立新视窗时，所要执行的shell
-S             指定screen作业的名称
-v 　          显示版本信息
-x 　          恢复之前离线的screen作业
-ls或-list 　  显示目前所有的screen作业
-wipe 　       检查目前所有的screen作业，并删除已经无法使用的screen作业
```

#### screen命令
1. 自定义shell脚本启动一个进程
```sh
screen_name="ngrok" # 创建了一个名为 my_screen 的窗
screen -dmS $screen_name

cmd="ngrok tcp 22";
screen -x -S $screen_name -p 0 -X stuff "$cmd"
screen -x -S $screen_name -p 0 -X stuff '\n'
```
2. 退出进程
```sh
screen -S session_name -X quit
```
其他
```
C-a ?       显示所有键绑定信息
C-a w       显示所有窗口列表
C-a C-a     切换到之前显示的窗口
C-a c       创建一个新的运行shell的窗口并切换到该窗口
C-a n       切换到下一个窗口
C-a p       切换到前一个窗口(与C-a n相对)
C-a 0..9    切换到窗口0..9
C-a a       发送 C-a到当前窗口
C-a d       暂时断开screen会话
C-a k       杀掉当前窗口
C-a [       进入拷贝/回滚模式
```
#### 退出screen
退出screen的作业时，有两种方式：
```
Crtl + a +d     保存进程并退出作业(程序在screen中继续运行，screen -ls 可查看)
Crtl + alt + a + d  进入后台运行进程
exit            退出作业和进程(程序终止，screen -ls 不可查看)
```
## 什么是Mosh
**Mosh**表示`移动Shell`(Mobile Shell)，是一个用于从客户端跨互联网连接远程服务器的命令行工具。它能用于`SSH`连接，但是比`Secure Shell`功能更多。它是一个类似于`SSH`而带有更多功能的应用。程序最初由Keith Winstein 编写，用于类Unix的操作系统中，发布于GNU GPL V3协议下。
**Mosh**最大的特点是基于UDP方式传输，支持在服务端创建一个临时的Key供客户端一次性连接，退出后失效；也支持通过SSH的配置进行认证，但数据传输本身还是自身的UDP方式。
* 会话的中断不会导致当前正在前端执行的命令中断，相当于你所有的操作都是在screen命令中一样在后台执行。
* 会话在中断过后，不会立刻退出，而是启用一个计时器，当网络恢复后会自动重新连接，同时会延续之前的会话，不会重新开启一个。

