---
title: 在slack上实现实时绘UML图
date: 2017-06-28 15:57:00
updated: 2018-09-22 21:18:59
comments: true
tags: [UML]
categories: 
- 学习笔记
keywords: 
permalink: 
---
[umlbot源码库](https://github.com/it-boyer/umlbot)
```puml
title 部署一台heroku机器人\n支持在slack窗口实时绘制uml图
center header
umlbot工具
endheader

[slack] as slack

cloud "heroku服务器"{
[umlbot] as umlbot
[heroku] as hero
umlbot -[#yellow]> hero: 部署机器人
hero -[#green]> umlbot: 原脚本
}

umlbot -[#green]-> slack: 绘制UML
slack -> hero: 输入hook用户数据\nTOKEN
hero --> slack: 机器人URL

'注释模块'
note right of umlbot : github源码库\nhero支持github直接部署
note left of heroku服务器 #white: 运行umlbot机器人\n 从slack获取源数据
note left of slack #red: 配置outgoing hook\n输出:token\n输入:机器人URL(s)

'接口模块'
()"注册" --> hero: 部署前提
()"部署" --> hero: 设置TOKEN

note left of "注册" #green: 需要翻墙\n"Please confirm you're not a robot."
note right of "部署" #red: 设置**TOKEN**位置\n通过readme提供的部署按钮\n来实现设置token

center footer
目前测试并没有实现slack绘制UML的效果
endfooter
```
