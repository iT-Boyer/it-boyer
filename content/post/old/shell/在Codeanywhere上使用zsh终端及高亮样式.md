---
title: 在Codeanywhere上使用zsh终端及高亮样式
date: 2018-09-13 11:25:14
updated: 2019-01-16 21:01:14
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
### 检查是否安装：
```
chsh
```
打印：
### 安装
Debian / Ubuntu 安装：
```
$ sudo apt-get install zsh
```
zsh直接启动：
```
zsh
```

### 安装oh-my-zsh样式工具
在hexoDeploy项目根目录执行：
```
sh Util/oh-my-zsh/installTemplate.sh
```
会提示`sed`文件修改失败的错误：
```
sed: can't read s#export ZSH=.*#export ZSH='/home/cabox/workspace/it-boyer.github.io/Util/oh-my-zsh'#g: No such file or directory
```
暂时通过手动来修改：
```
vi ../oh-my-zsh/templates/zshrc.zsh-template
```
修改如下：
```
export ZSH='/home/cabox/workspace/it-boyer.github.io/Util/oh-my-zsh'
```
### 安装space-vim工具
1. 执行
```
sh Util/space-vim/Manualinstall.sh
```
创建替身失败的提示：
```
/home/cabox/workspace/it-boyer.github.io
已经存在
Util/space-vim/Manualinstall.sh: 18: Util/space-vim/Manualinstall.sh: Syntax error: "(" unexpected
```
手动解决创建替身的：
```
$ ln -fs /home/cabox/workspace/it-boyer.github.io/Util/space-vim/init.vim ~/.vimrc
$ ln -fs /home/cabox/workspace/it-boyer.github.io/Util/space-vim/init.spacevim ~/.spacevim
```
### 安装vim插件
启动vim工具，终端自动安装相关插件。
