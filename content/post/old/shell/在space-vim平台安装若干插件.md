---
title: 在space-vim平台安装若干插件
date: 2017-08-17 14:26:46
updated: 2018-09-22 21:18:50
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
---
## space-vim
首次启用一个 layer，需要执行 SPC f R, 或者 :so $MYVIMRC, 重新加载 .vimrc 并执行 :PlugInstall 安装所需的相关插件 。或者退出重新打开 vim, vim-plug 将会检测并自动安装缺失的插件。
基于vim-plug的插件管理平台
[vim-plug命令工具](https://github.com/junegunn/vim-plug#commands)
### Commands

| Command                             | Description                                                        |
| ----------------------------------- | ------------------------------------------------------------------ |
| `PlugInstall [name ...] [#threads]` | Install plugins                                                    |
| `PlugUpdate [name ...] [#threads]`  | Install or update plugins                                          |
|`PlugClean[!]`                      | Remove unused directories (bang version will clean without prompt) |
| `PlugUpgrade`                       | Upgrade vim-plug itself                                            |
| `PlugStatus`                        | Check the status of plugins                                        |
| `PlugDiff`                          | Examine changes from the previous update and the pending changes   |
| `PlugSnapshot[!] [output path]`     | Generate script for restoring the current snapshot of the plugins  |

###  `Plug` options

| Option                  | Description                                      |
| ----------------------- | ------------------------------------------------ |
| `branch`/`tag`/`commit` | Branch/tag/commit of the repository to use       |
| `rtp`                   | Subdirectory that contains Vim plugin            |
| `dir`                   | Custom directory for the plugin                  |
| `as`                    | Use different name for the plugin                |
| `do`                    | Post-update hook (string or funcref)             |
| `on`                    | On-demand loading: Commands or `<Plug>`-mappings |
| `for`                   | On-demand loading: File types                    |
| `frozen`                | Do not update unless explicitly specified        |
## 安装objc 自动提示插件YouCompleteMe
[YouCompleteMe命令工具](https://github.com/Valloric/YouCompleteMe#commands)
1. 在spacevim添加
使用space-vim封装的layer
```
Layer 'ycmd'         "语法自动补齐
```
 YouCompleteMe 安装位置： `~/.vim/plugged/YouCompleteMe` 。
 
2. 编译 YCM
在使用space-vim平台上，使用layer方式安装会执行如下编译操作：
```
!./install.py --clang-completer
```
详见脚本：space-vim/layers/+tools/ycmd/packages.vim

参考：[征服恐惧！用 Vim 写 iOS App](http://www.cocoachina.com/ios/20170224/18751.html)
```
brew install cmake
./install.py --clang-completer --system-libclang
```
`--clang-completer`: 告诉脚本需要 clang 的支持
`--system-libclang`: 告诉编译脚本使用系统的 clang，因为之前 clang 升级 4.0 的时候，并没有已经编译好的包给我下载，所以这里不用系统 clang 的话，编译脚本会下载一个 clang 3.0，这样就无法支持 iOS 10.0 以后的 sdk 了，因为 iOS 10.0 以后的 sdk 为了支持 swift 引入了一些 clang 3.0 不支持的新语法，所以这里要加上 --system-libclang。

3. FlagsForFile脚本获取编译参数
YCMD 是通过每个项目路径下的 `.ycm_extra_conf.py` 脚本文件，定义了`FlagsForFile` 的函数来获取某一个特定文件需要的编译参数，一般情况下大部分文件的编译参数是相同的。

2. 安装vim插件
使用vim-plug安装
```
Plug 'keith/sourcekittendaemon.vim'
```
NOTE: This plugin doesn't provide Swift runtime files. If you'd like those checkout swift.vim

### AsyncRun shell command
编辑器命令:AsyncRun + shell命令即可在后台执行shell命令，打开quickfix就可以实时查看执行结果了。也可以通过添加配置的方式来实现开始执行命令的时候自动打开quickfix窗口：
```
 :copen //打开脚本运行日志窗口 
 :AsyncRun git status  //异步执行shell脚本
```
