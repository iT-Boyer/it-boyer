+++
title = "hugo中集成Fuse.js实现站内搜索"
lastmod = 2021-05-07T21:18:29+08:00
draft = false
author = "iTBoyer"
tags = ["hugo"]
categories = ["博客站务"]
+++

[5分钟给Hugo博客增加搜索功能 :: /dev/ttyS3 — 回首向来萧瑟处 也无荒野也无灯](https://ttys3.dev/post/hugo/hugo-fast-search/)  
[Fast, instant client side search for Hugo static site generator · GitHub](https://gist.github.com/cmod/5410eae147e4318164258742dd053993)  

配置config.toml outputs:支持json  
在layouts/\_default/新建 index.json  
在layouts/\_default/新建 baseof.html 先拷贝even主题下的文件，在把搜索样式添加到文件末尾在static中添加js脚本在fastsearch.js中配置\*\*CMD+/\*\*显示隐藏快捷键
