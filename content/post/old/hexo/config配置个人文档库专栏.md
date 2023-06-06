---
title: config配置个人文档库专栏
date: 2018-10-20 13:54:16
updated: 2018-10-20 13:54:16
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
<!--github库卡片-->
{% github it-boyer blogDocs 978ed39 width = 30% %}
### 忽略配置
文档库主要整理kindle读书笔记，jazzy文档相关html页面，放在hexo中不需要经过md转换html过程。故需要通过hexo的`skip_render`配置机制，来屏蔽一些目录/文件等。
```
skip_render: ["*.html","docs/*/*"]
```
这样，在hexo g过程，跳过这些文件，目录的编辑过程，直接拷贝到public中发布。
[skip_render参考](https://github.com/hexojs/hexo/issues/1146#issuecomment-88380140)
### docs配置
1. 位置考虑
`docs`放在在source目录下，目录结构：
```
├── souce
    ├── _post       //博客
    ├── tags        //标签
    ├── categories  //分类
    ├── docs        //文档库
        ├── index.md
        └── kindle笔记
```
其他配置可以按照`tag`,`categories`相关配置，显示在网页中。
2. 版本控制
考虑`docs`文档库的容量递增，选择`git submodule`来管理`docs`文档库，集成到hexo主库中。

