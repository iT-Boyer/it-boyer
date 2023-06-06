---
title: heroku的SSH终端命令
date: 2018-09-05 14:03:39
updated: 2018-10-21 22:42:59
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## 启动heroku 终端
[Heroku Exec (SSH Tunneling)]((https://devcenter.heroku.com/articles/exec))
每个`Heroku exec`连接最多持续一个小时。一小时后，您可能需要重新连接。`Heroku exec`在对Shield Private Spaces是不限制的。

先在该目录初始化环境
```
heroku create myhexo
```
1. 创建
```
heroku ps:exec
```
1. bash
```
heroku run bash
//
Heroku ps:stop run #停止所有的dynos
再执行heroku run bash
```
3. 查看正在运行的bash
```
Heroku run:detached ls
```
4. 退出
```
Heroku dyno:kill dyno
```
5. 重启
```
Heroku dyno:restart [dyno]
```
