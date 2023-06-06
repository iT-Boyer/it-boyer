---
title: pyenv切换python的版本
date: 2018-05-29 10:33:30
updated: 2018-05-29 12:26:48
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: 
permalink: 
---
## 问题引入
[在hexo 项目中使用npm 配置环境，出现错误：gyp ERR! configure error](https://stackoverflow.com/questions/32702098/err-stack-error-command-failed-python2-c-import-platform)

pyenv是python的多版本管理包，实现互相独立、互不干扰的python环境配置。

## 安装pyenv

安装电脑是mac，所以理所当然的使用神器：homebrew
```sh
brew install pyenv
```
安装界面略过，安装结束后，系统提示如下：
```sh
==> Caveats
To use Homebrew's directories rather than ~/.pyenv add to your profile:
export PYENV_ROOT=/usr/local/var/pyenv

To enable shims and autocompletion add to your profile:
if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
```
根据此提示，在bash的配置文件（由于我的bash是oh my zsh，所以我的配置文件为~/.zshrc）中添加以下两行代码：
```
export PYENV_ROOT=/usr/local/var/pyenv
if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
```
## 使用pyenv

#### 安装指定版本的python
显示所有可以安装的python版本软件，如2.1.3...3.5.1等。
```sh
pyenv install -l
pyenv install 2.7.11 -v
```
`-v`表示在显示安装过程。
安装完成后，2.7.11版本在系统中的存放位置为：
`/usr/local/var/pyenv/versions/2.7.11`

### 为项目配置python环境
接下来进入开发项目的主文件夹，如`~/Desktop/Python/TWD`，输入如下命令：
```
pyenv local 2.7.11
```
即在当前文件夹下配置完成python的开发环境。接下来可通过pip安装开发过程中的各种包。
## 其他
### 1.显示所有安装的python版本
```
pyenv versions
```
### 2.切换python版本
要切换python 版本，可以使用如下命令：
```
pyenv global <version>
```
比如，我使用以上命令pyenv global 2.7.11后，系统默认的python版本即为2.7.11，在命令行输入python后，进入的就是2.7.11的shell，不再是system的shell。

### 3.切换python shell版本
若不使用pyenv global命令实现python shell版本切换，可以使用如下命令：
```
pyenv shell <version>
```
比如，我使用pyenv shell 2.7.11后，在命令行输入python，进入的是2.7.11的shell。此时系统的默认python版本也变成了2.7.11，如下所示：

[转自](https://www.jianshu.com/p/bcb3f1be9073)
