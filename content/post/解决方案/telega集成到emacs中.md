+++
title = "emacs中使用telega客户端"
description = "记录在emacs中安装telega的步骤和基本使用"
date = 2021-10-13T22:07:00+08:00
publishDate = 2021-10-13T21:41:00+08:00
lastmod = 2021-10-13T22:08:49+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## 安装tdlib {#安装tdlib}

安装方式有两种，一种使用brew, 一种使用源码编译tdlib  

在使用 `brew install tdlib` 之后，显示版本过时，无法使用，只能借助编译方式获得最新版本。  

1.  编译教程：[TDLib build instructions](https://tdlib.github.io/td/build.html?language=Swift)  
    
    {{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       xcode-select --install
       /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
       brew install gperf cmake openssl
       git clone https://github.com/tdlib/td.git
       cd td
       rm -rf build
       mkdir build
       cd build
       cmake -DCMAKE_BUILD_TYPE=Release -DOPENSSL_ROOT_DIR=/usr/local/opt/openssl/ -DCMAKE_INSTALL_PREFIX:PATH=../tdlib ..
       cmake --build . --target install
       cd ..
       cd ..
       ls -l td/tdlib
    {{< /highlight >}}
    
    编译成功之后，可以会在 `td/tdlib` 目录下找到最新版本库文件。

2.  使用 `brew info tdlib` ，查看安装目录

3.  将brew 版本 替换为最新版本 tdlib  
    
    {{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       # 拷贝 最新版库1.7.8拷贝到tdlib目录，
       cp -r ~/hsg/td/tdlib /usr/local/Cellar/tdlib/1.7.8
       # 更新 1.7.0 软连接 删除久版本1.7.6
       cd /usr/local//Cellar/tdlib
       ln -sf 1.7.8 1.7.0
       rm -rf 1.7.6
       # 删除旧版本库libtdjson.1.7.6.dylib 软连接
       cd /usr/local/lib
       rm libtdjson.1.7.6.dylib
       # 创建新版软连接
       ln -sf ../Cellar/tdlib/1.7.0/lib/libtdjson.1.7.8.dylib libtdjson.1.7.8.dylib
    {{< /highlight >}}

4.  回到telega 执行：M-x telega
