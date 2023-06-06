+++
title = "使用Fuse.js实现hugo站内搜索功能"
description = "在站内快速定位知识点"
date = 2021-08-03T14:00:00+08:00
publishDate = 2021-08-03T13:50:00+08:00
lastmod = 2021-08-03T14:00:29+08:00
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [hugo中集成Fuse.js实现站内搜索](#hugo中集成fuse-dot-js实现站内搜索)

</div>
<!--endtoc-->


## hugo中集成Fuse.js实现站内搜索 {#hugo中集成fuse-dot-js实现站内搜索}

[5分钟给Hugo博客增加搜索功能 :: /dev/ttyS3 — 回首向来萧瑟处 也无荒野也无灯](https://ttys3.dev/post/hugo/hugo-fast-search/)  
[Fast, instant client side search for Hugo static site generator · GitHub](https://gist.github.com/cmod/5410eae147e4318164258742dd053993)  

-   [X] 配置config.toml outputs:支持json
-   [X] 在layouts/\_default/新建 index.json
-   [X] 在layouts/\_default/新建 baseof.html 先拷贝even主题下的文件，在把搜索样式添加到文件末尾
-   [X] 在static中添加js脚本
-   [X] 在fastsearch.js中配置\*\*CMD+/\*\*显示隐藏快捷键
