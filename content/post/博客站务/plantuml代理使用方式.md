+++
title = "借助plantuml代理服务实现在markdown中绘制原型图"
description = "使用代理绘制原型图，实现在markdown中插入原型图"
date = 2021-08-09T22:33:00+08:00
publishDate = 2021-08-09T22:11:00+08:00
lastmod = 2021-10-13T12:46:24+08:00
tags = ["uml"]
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

## 目的 {#目的}

了解plantuml server 强大的代理功能，实现在github上插入uml原型图。目前使用的方式，在org中插入脚本，生成本地文件，再加载到org中，会产生图片附件，需要维护和空间的占用。经过学习，可以通过代理方式，只需要指定uml的源文件，服务器就可以返回已经绘制原型图。 [The servlet for server side](https://plantuml.com/zh/server#f2965a0e-2e2a-d8e4-7619-b00fed70dc4f)  


## 代理服务介绍 {#代理服务介绍}

通过代理服务，PlantUML 服务器可以从远程plantuml文件中获取uml的源码。  

代理服务使用以下 URL 方案： `/plantuml/proxy?src=RESOURCE&idx=INDEX&fmt=FORMAT`  

-   `RESOURCE` ： uml源码（如：@startxxx和@endxxx标签）远程文件的完整 URL ，它可以是一个 `.html` 或一个 `.txt` 文件。
-   `INDEX` 是可选的，它指定当远程文档中有多个图描述时，将解析的图描述的出现（从 0 开始）。它默认为零。
-   `FORMAT` 是可选的，它指定要返回的格式。支持的值是： `png` ， `svg` ， `eps` ， `pstext` 和 `txt` 。默认为 `png` 。

例如: `http: www.plantuml.com/plantuml/proxy?src=https: raw.github.com/plantuml/plantuml-server/master/src/main/webapp/resource/test2diagrams.txt` >注意远程文档的地址是作为参数指定的，所以不需要对URL进行URL编码。  


## 其他资料 {#其他资料}

[git - How to integrate UML diagrams into GitLab or GitHub - Stack Overflow](https://stackoverflow.com/questions/32203610/how-to-integrate-uml-diagrams-into-gitlab-or-github)  

[Markdown native diagrams with PlantUML &vert; Andreas' Blog](https://blog.anoff.io/2018-07-31-diagrams-with-plantuml) 作者的两个案例： [GitHub - anoff/plantbuddy: nodeMCU based moisture monitoring for plants 🌱 wit...](https://github.com/anoff/plantbuddy#main-features) [GitHub - devradar/devradar: Competence Management for developers](https://github.com/anoff/techradar#design)
