---
title: hexo博文置顶方法
date: 2018-05-31 11:44:33
updated: 2018-09-22 21:18:48
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
修改 `hero-generator-index` 插件，把文件：`node_modules/hexo-generator-index/lib/generator.js` 内的代码替换为：
```js
'use strict';
var pagination = require('hexo-pagination');
module.exports = function(locals){
var config = this.config;
var posts = locals.posts;
    posts.data = posts.data.sort(function(a, b) {
            if(a.top && b.top) { // 两篇文章top都有定义
                if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
                else return b.top - a.top; // 否则按照top值降序排
            }
            else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面（这里用异或操作居然不行233）
                return -1;
            }
            else if(!a.top && b.top) {
                return 1;
            }
            else return b.date - a.date; // 都没定义按照文章日期降序排
        });
    var paginationDir = config.pagination_dir || 'page';
    return pagination('', posts, {
        perPage: config.index_generator.per_page,
        layout: ['index', 'archive'],
        format: paginationDir + '/%d/',
        data: {
            __index: true
        }
    });
};
```
在文章中添加 `top` 值，数值越大文章越靠前:
```
title: 解决Charles乱码问题
date: 2017-05-22 22:45:48
tags: 技巧
categories: 技巧
copyright: true
top: 100
```
转：[hexo的next主题个性化配置教程](https://segmentfault.com/a/1190000009544924)
