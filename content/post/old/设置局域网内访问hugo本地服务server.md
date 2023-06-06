+++
title = "设置局域网内访问 hugo 本地服务 server"
date = 2019-08-09T19:53:00+08:00
lastmod = 2021-05-07T13:23:40+08:00
tags = ["hugo"]
categories=["博客站务"]
draft = false
author = "iTBoyer"
+++

## 场景说明 {#场景说明}

hugo配合emacs编写博客，借助emacs中org-mode的强大功能，对知识逻辑梳理和层级整理的神级操作，结合hugo+githubPage静态网页的支持，能够很好的将emacs本地知识体系分享到网络上。hugo的对知识点的可读性和体系，都有一个套完整的解决方案。


## 目的 {#目的}

1.  hugo支持本地部署网页服务，可以实时预览博客
2.  emacs上org-mode支持日程管理和代码高亮，有ox-hugo的插件的支持，能够在日常管理中，实现知识输入的需求。


## server命令的介绍 {#server命令的介绍}

本章先解下hugo的server命令。在终端可以通过help命令得到详细的帮助的信息：

{{< highlight sh >}}
hugo help server
{{< /highlight >}}

1.  如果想在本地调试网页，就需要启动hugo服务，直接通过本地IP来访问网页：

    {{< highlight sh >}}
        hugo server
        Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
    {{< /highlight >}}

    以上命令，默认绑定的iP地址是：127.0.0.1，这样只有在本机才能访问到hugo服务。
2.  如果想在该局域网内的其他客户端（手机/iPad）上访问电脑上hugo服务，就需要设置绑定参数：--bind为0.0.0.0。这样局域网内所有客户端的地址都可以访问服务。

    {{< highlight sh >}}
        hugo server --bind="0.0.0.0"
        Web Server is available at http://localhost:1313/ (bind address 0.0.0.0)
    {{< /highlight >}}
3.  -p设置服务端口号，也是一个常用的参数，默认1313，有时会随机监听一个端口号，这时就可以通过-p参数来指定一个固定的端口。

    {{< highlight sh >}}
        hugo server --bind="0.0.0.0" -p 1234
        Web Server is available at http://localhost:1234/ (bind address 0.0.0.0)
    {{< /highlight >}}
