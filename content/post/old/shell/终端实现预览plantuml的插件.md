---
title: 终端实现预览plantuml的插件
date: 2018-05-29 11:32:04
updated: 2019-01-16 21:01:14
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
---
{% github weirongxu plantuml-previewer.vim  bf4b3e5 width = 30% %}
## 安装依赖
```sh
Plug 'it-boyer/plantuml-syntax'  "plantuml语法高亮 Plug 'aklt/plantuml-syntax'
Plug 'tyru/open-browser.vim'
Plug 'weirongxu/plantuml-previewer.vim' "在线工具：http://sujoyu.github.io/plantuml-previewer/
```
### Graphviz
[下载地址](https://www.graphviz.org/download/)
```
brew install graphviz
```
### 打开浏览器safari插件工具
open-browser.vim
### 语法高亮插件
aklt/plantuml-syntax (vim syntax file for plantuml)


## 使用
### 创建uml文件
```
vi test.uml
```

### 预览uml图
通过命令打开浏览器预览界面
```
:PlantumlOpen
```
在vi中执行保存命令`:w`,预览界面会自动刷新

