---
title: 安装Material主题
date: 2018-10-12 19:33:01
updated: 2018-10-12 19:56:59
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
[Hexo + Material + Github 搭建博客与配置](https://zdran.com/20180326.html)
<!--github库卡片-->
{% github viosey hexo-theme-material d93c5a8 width = 30% %}

注意！ 在主题的开发迭代过程中，主题的配置文件模板 可能会改动。为了避免使用 git pull 更新主题的用户出现冲突，我们将 主题配置文件模板 命名为 `_config.template.yml`。配置主题时，你应该拷贝一份 `_config.template.yml` 并将其重命名为 `_config.yml`。

## 遇到的问题
[issues/688](https://github.com/viosey/hexo-theme-material/issues/688)
在主题文件夹下新建一个_config.yml文件，并将_config.template.yml里的配置复制到_config.yml文件。
修改layout/_widget/dnsprefetch.ejs文件。修改内容如下：
```
<% } else if(theme.comment.use.startsWith("disqus")) { %>
// to
<% } else if(theme.comment.use && theme.comment.use.startsWith("disqus")) { %>
```

```
ERROR /Users/kai/Code/hexoBlog/themes/material/layout/layout.ejs:3
1| 
2| <html style="display: none;" <% if(config.language !== null) { %>lang="<%- config.language.substring(0,2) %>"<% } %>>

3| <%- partial('_partial/head') %>
4|
5| <% if(page.layout === 'gallery') { %>
6|
/Users/kai/Code/hexoBlog/themes/material/layout/_partial/head.ejs:22
20|
21|

22| <%- partial('_widget/dnsprefetch') %>
23|
24| 
25|
/Users/kai/Code/hexoBlog/themes/material/layout/_widget/dnsprefetch.ejs:2
1|

2| <% if(theme.vendors.materialcdn) { %>
3| 
4| <% } %>
5| <% if( (theme.leancloud.enable === true) || (theme.comment.use == "valine") ) { %>
```
