+++
title = "在 emacs 写博客精进手册:搭建 ox-hugo 测试用例站点"
description = "Test post to test another ox-hugo test."
date = 2021-05-10T12:30:00+08:00
lastmod = 2021-05-14T13:46:54+08:00
tags = ["hugo"]
categories = ["博客站务"]
draft = false
author = "iTBoyer"
authors = ["iTBoyer"]
+++

ox-hugo 帮助用户很好的使用，专门提供了一个测试用例站点.  
下面介绍下如何在本地轻松搭建一个测试用例站点，对于刚刚开始使用 emacs 写博客的新手,有很大的帮助.  

你在编写博客遇到 ox-hugo 语法不确定时,都可以在测试用例站点,检索这些基础语法的实际案例:  
>1. 单行字符串样式:加粗,斜体,高亮等样式对照表 Formatting  
>2. 在 emacs 中常用的字体语法: Nested bold and italics  
>3. 自定义 Front matter :Custom front matter  

都可以在这个文件[ox-hugo/all-posts.org](https://hub.fastgit.org/kaushalmodi/ox-hugo/blob/be7fbd9f164d8937b2628719e21e8e6b4827e638/test/site/content-org/all-posts.org)下找到真实语法案例  


## 搭建过程 {#搭建过程}

[Hugo test site for this package — ox-hugo - Org to Hugo exporter](https://ox-hugo.scripter.co/doc/tests-site/#alternative-way)  

1.  clone 项目到本地  
    
    ```shell
       git clone --recurse-submodules -j8 https://github.com/kaushalmodi/ox-hugo
    ```
2.  确保安装 pandoc: `brew search pandoc`
3.  部署  
    
    ```shell
       cd ox-hugo/test/site/
       hugo server -D --navigateToChanged
       open http://localhost:1234 #预览
    ```
4.  使用 emacs 导出 org  
    在 emacs 中打开 all-posts.org 文件，  
    
    ```shell
       emacs ox-hugo/test/site/content-org/all-posts.org
    ```
    
    并执行导出命令：spc m e H A,导出目录：~test/site/content/posts/~
5.  在浏览器上访问上面的 hugo 站点，会看到新增的内容。
