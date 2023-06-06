+++
title = "创建单个 swift 可执行文件"
lastmod = 2020-09-13T22:33:34+08:00
tags = ["swift"]
categories=["学习笔记"]
draft = false
author = "iTBoyer"
#password = 1234
+++

## xcrun/swift 命令工具 {#xcrun-swift-命令工具}

1.  第一种  
    vi test.swift  
    \#!/usr/bin/env xcrun swift  
    print("hello")  
    
    $ chmod 755 test.swift  
    $ ./test.swift
2.  第二种  
    $ cat <<EOF > script  
    \#!/usr/bin/swift  
    print("Hi!")  
    EOF  
    $ chmod u+x script  
    $ ./script  
    Hi!


## swift-sh 支持库依赖 {#swift-sh-支持库依赖}

1.  安装命令  
    brew install swift-sh
2.  使用  
    $ cat <<EOF > script  
    \#!/usr/bin/swift sh  
    import PromiseKit  // @mxcl ~> 6.5  
    print(Promise.value("Hi!"))  
    EOF  
    $ chmod u+x script  
    $ ./script  
    Promise("Hi!")
3.  升级  
    $ git clone <https://github.com/mxcl/swift-sh.git>  
    $ cd swift-sh  
    $ swift build  
    把编译后的可执行文件 swift-sh，拷贝到 dotfiles/swift-sh/bin/swift-sh

/\*\*  
 ^^Import local files  <https://github.com/mxcl/swift-sh/issues/17>  
 ^^ import Foobar // ./test2.swift  
 ^^仅支持导入由 SPM 管理的项目（Package.swift）  
 ^^暂时不支持 swift 文件导入方式，不能像 ruby 一样加载另一个 rb 文件  
 \*/
