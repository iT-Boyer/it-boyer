---
title: 博客生成restful风格的json数据源
date: 2018-10-12 15:36:18
updated: 2018-10-12 15:36:18
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: 
permalink: 
copyright: 
password: 
top:   
---

[hexo-generator-restful](http://npm.taobao.org/package/hexo-generator-restful)
```
## API接口：以下为默认配置，属性值为 false 表示不生成。
restful:  #http://npm.taobao.org/package/hexo-generator-restful
# site 可配置为数组选择性生成某些属性
# site: ['title', 'subtitle', 'description', 'author', 'since', email', 'favicon', 'avatar']
site: false        # hexo.config mix theme.config
posts_size: 10    # 文章列表分页，0 表示不分页
posts_props:      # 文章列表项的需要生成的属性
title: false
slug: false
date: false
updated: false
comments: false
path: false
excerpt: false
cover: false      # 封面图，取文章第一张图片
content: false
keywords: false
categories: false
tags: false
categories: false  # 分类数据
tags: false        # 标签数据
post: false        # 文章数据
pages: false      # 额外的 Hexo 页面数据, 如 About
```
