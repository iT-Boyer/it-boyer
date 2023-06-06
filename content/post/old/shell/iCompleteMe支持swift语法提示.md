---
title: iCompleteMe支持swift语法提示
date: 2018-08-10 16:40:19
updated: 2018-08-10 18:42:08
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
<!--github库卡片-->
{% github jerrymarino iCompleteMe ad0e1fe width = 30% %}
iCompleteMe是基于YouCompleteMe。在花了一年多的时间试图实现对YouCompleteMe的快速支持之后，发现在YCM无法支持swift自动补齐。
iCompleteMe实现的行为对于Swift的补齐提示。iCompleteMe的核心子系统只与Swift一起工作。代码基占用的空间要小得多，这使得在CI上更容易安装、更容易理解和更稳定(理论上)。

### 在space-vim中添加插件支持
```
Plugin 'jerrymarino/iCompleteMe'
```
安装：
```
cd ~/.vim/plugged/iCompleteMe/
brew install cmake
./install.py
```
然后 `<Leader> f R` 使配置生效，并执行   ` :PlugInstall `进行安装.
