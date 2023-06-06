---
title: 同步印象笔记到hexo博客
date: 2018-10-18 20:25:49
updated: 2018-10-19 16:16:34
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
{% github everblogjs everblog-adaptor-hexo 3c081de width = 30% %}
{% github everblogjs everblog b1832a6 width = 30% %}
[Everblog ——使用 Evernote 写博客](https://www.v2ex.com/t/418632)
[印象笔记+hexo搭建自己的个人博客](https://itgoyo.github.io/8888/08/08/印象笔记-hexo搭建自己的个人博客/)
[申请印象笔记token](https://dev.yinxiang.com/doc/articles/dev_tokens.php)
邮件申请：
```
欢迎使用印象笔记开发者Token功能，麻烦你回复这封邮件，在收到你的确认邮件后，我们会为你开启开发者Token权限，谢谢。

注：回复邮件的邮箱需要和帐户的注册邮箱地址保持一致，如果当前地址不是帐户注册邮箱，建议使用帐户注册邮箱直接发送邮件到 online-help@yinxiang.com ，并说明需要开启Token即可。
```
使用 Hexo 主题
图文步骤如下：
在印象笔记操作
创建 _config.yml 
创建一些笔记  
在hexo中执行
运行 DEBUG=* everblog start 构建并打开构建成功后的主页  
使用 Hexo 主题完整步骤如下：
```
$ npm i hexo-cli -g # 全局安装 hexo-cli
$ hexo init myblog # 初始化一个 hexo 项目
$ cd myblog && npm i # 安装依赖
$ npm i everblog -g # 全局安装 everblog
$ npm i everblog-adaptor-hexo --save # 在当前 hexo 项目下安装 adaptor
$ echo "module.exports = require('everblog-adaptor-hexo')" > index.js # 在当前 hexo 项目下创建 index.js ，引入 adaptor
$ DEBUG=* everblog build # 使用 everblog 构建 hexo 所需文件
$ hexo server # 启动 hexo
$ open http://localhost:4000/ # 浏览器打开博客主页
```
需要安装node8+
[详见issue-371806541](https://github.com/everblogjs/everblog/issues/11#issue-371806541)
