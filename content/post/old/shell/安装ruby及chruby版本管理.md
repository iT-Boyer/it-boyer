---
title: 安装ruby及chruby版本管理
date: 2018-10-19 15:58:23
updated: 2018-10-19 16:16:34
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
<!--github库卡片-->
{% github postmodern ruby-install ea2b8bb width = 30% %}

## 安装工具ruby-install
```
$ brew install ruby-install
```
### 安装指定 Ruby 版本
方式一：使用`Homebrew`安装
```
$ ruby-install ruby 2.4.1
$ ruby-install --system ruby  #覆盖系统版本
```
但Mac最新系统安装Xcode时已经没有Command Line工具，需要单独安装。安装命令行：`xcode-select --install`

### 安装chruby管理工具
```
$ brew install chruby
```
## 管理ruby版本
1. 预览安装的版本
```
$ chruby
    ruby-1.9.3-p392
    jruby-1.7.0
    rubinius-2.0.0-rc1
```
2. 切换为使用版本
```
$ chruby 1.9.3 #切换操作
$ chruby
* ruby-1.9.3-p392  #当前使用的版本
    jruby-1.7.0
    rubinius-2.0.0-rc1
```
3. 智能切换支持
让chruby在`cd`不同项目目录时，自动切换Ruby的当前版本，`load auto.sh` in `~/.bashrc` or `~/.zshrc`:
```
source /usr/local/share/chruby/chruby.sh
source /usr/local/share/chruby/auto.sh
```
>OSX does not automatically execute `~/.bashrc`, instead try adding to `/etc/bashrc`.

4. 设置默认的ruby
通过设置启动项来设置默认的ruby版本，在 `~/.bash_profile` or `~/.zprofile`设置:
```
chruby ruby-1.9
```
如果终端已经设置自启动项切换 `chruby.sh` and/or `auto.sh` , 只需要在`~/`目录新建`.ruby-version`:
```
echo "ruby-1.9" > ~/.ruby-version
```

