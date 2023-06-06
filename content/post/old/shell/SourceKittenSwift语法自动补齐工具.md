---
title: SourceKittenSwift语法自动补齐工具
date: 2017-06-29 10:08:50
updated: 2018-09-22 21:18:49
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
---
  
vim插件工具：Vundle

主题色：
|组合键 |   Description|
空格 + T + n|  随机切换颜色主题。
SPC T s    |使用unite buffer方式切换一个主题

## 插件
###  [Unite](https://github.com/Shougo/unite.vim)
预定义操作命令
Unite或unite.vim插件可以搜索和显示信息，例如：任意源文件、缓冲区的，最近使用的文件或记录。可以直接运行在Unite窗口中显示的几个预设操作。

### neocomplete
一个自动补全的插件，使用TAB或ENTER键来选择。
同时，它又额外附带了补全代码段的特性（模版补全），要想使用这种特性，必须安装另外的插件neosnippet或者ultisnips。
![](http://img.blog.csdn.net/20161026000011614?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### [NERD Commenter](https://github.com/scrooloose/nerdcommenter)
快速注释/解开注释

### Goyo and Limelight
干净模式和背景虚化。两者配合使用，效果非常好。vim 也可以很文艺

### [Bookmarks](http://blog.csdn.net/mdl13412/article/details/44081509)
插件旨在解决 Vim 自带书签无法高亮、无法持久化、难于记忆的问题，而且解决的非常漂亮. 下面列出其主要特性:

单行书签切换 ⚑
单行的注释(说明)书签 ☰
在 quickfix 窗口中访问所有书签
书签自动保存，下次开启自动加载
针对工作目录的独立书签(可选)
高度可定制
可以和 Unite 插件的 quickfix 结合
不依赖 Vim 自身的 marks


### Gita
git插件

### 其他插件
Key    Mode    Action
<leader>+gu    Normal    Open undo tree
<leader>+i    Normal    Toggle indentation lines
<leader>+j    Normal    Start smalls
<leader>+r    Normal    Quickrun
<leader>+?    Normal    Dictionary
<leader>+W    Normal    Wiki
<leader>+K    Normal    Thesaurus
