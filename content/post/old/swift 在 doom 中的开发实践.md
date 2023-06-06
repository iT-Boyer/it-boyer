+++
title = "swift 的 doom 环境"
lastmod = 2020-09-13T22:40:23+08:00
tags = ["doom","swift"]
categories=["解决方案"]
draft = false
author = "iTBoyer"
#password = 1234
+++

## 安装 swift-mode {#安装-swift-mode}


#### swift 自动补齐相关插件 swift-mode {#swift-自动补齐相关插件-swift-mode}

-   State "DONE"       from "DELEGATED"  <span class="timestamp-wrapper"><span class="timestamp">[2019-12-11 三 09:30]</span></span>  
    [终端支持 swift 自动补齐](_post/shell/终端支持 swift 自动补齐_)  
    [flycheck-swift](<https://github.com/swift-emacs/flycheck-swift>)  
    [nathankot/company-sourcekit](<https://github.com/nathankot/company-sourcekit>)  
    [swift-emacs/swift-mode](<https://github.com/swift-emacs/swift-mode>)


## 创建可执行项目 {#创建可执行项目}

spi --type executable  
上述命令\`spi\`的区别是\`Package.swift\`不同，Source 中多一个 main.swift 文件。当 source 中存在 main.swift 时，\`spx\`生成的 xcodeproj 中 target 清单，会显示可执行图标。  


## 在 emacs 中进入项目目录 {#在-emacs-中进入项目目录}

新建项目目录：SPC p a hello  
切换项目：SPC p p hello  


## 编辑 swift 代码，执行 lldb 调试 swift {#编辑-swift-代码-执行-lldb-调试-swift}


### swift 终端： {#swift-终端}

启动： M-x: run-repl  
退出： :exit RET  


### lldb 联调程序(仅在单个 target 项目中测试通过) {#lldb-联调程序--仅在单个-target-项目中测试通过}

启动：M-x: debug-swift-mode  
退出：exit RET  
使用该调试方式，无法支持多个 target 的项目，即  
Package.swift 中有多个 taget 时，debug 失败。  

-    在 swift-mode 如何多个 target

    不知道如何指定 target 调试  
    [swift-mode暂不支持多个target](https://github.com/swift-emacs/swift-mode/issues/162)  

-    断点使用 help breakpoint

    断点使用文档： help b  
    熟悉一个 filter 命令，是哪个命令的
