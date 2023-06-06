---
title: zsh插件之gi使用说明
date: 2018-10-09 12:04:55
updated: 2018-10-15 08:26:32
comments: true
tags: [zsh]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github joeblau gitignore.io a09921d width = 30% %}
## 配置zsh支持插件`gi`命令
1. 先浏览`oh-my-zsh/plugins`目录下支持所有插件,找到`gi`命令的插件名称：`gitignore`,即目录名称。
2. 把`gitignore`添加到`zshrc.zsh-template`的插件激活的清单中：
```
# 插件设置，如果添加太多启动速度会比较慢
plugins=(git gitignore ruby autojump osx mvn)
```
## `gi`清单命令使用 
再次打开zsh窗口会激活`gi`命令
* list命令
打印出[gitignore.io](https://www.gitignore.io/)官网支持语种的所有模版：
```
$ gi list
```
* 系统全局清单
在`.gitignore_global`文件中添加，忽略当前操作系统中某个`IDE`工具的忽略清单:
```
$ gi linux,eclipse >> ~/.gitignore_global
```
* Project项目忽略清单
在`.gitignore`文件中添加，配置项目中使用的`源码语言`的相关忽略清单:
```
$ gi java,python >> .gitignore
```

