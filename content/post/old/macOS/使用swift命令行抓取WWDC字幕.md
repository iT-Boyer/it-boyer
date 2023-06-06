---
title: 使用swift命令行抓取WWDC字幕
date: 2018-11-18 19:10:49
updated: 2018-11-18 19:10:49
comments: true
tags: [swift]
categories:
- 项目总结
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
<!--github库卡片-->
{% github it-boyer WWDC-Subtitles 84878dc width = 30% %}
平时没有那么大段的时间能去看这些 session 的视频，想先通过字幕能了解到 session 中的内容，所以搜罗了网上大牛抓取WWDC字幕的相关实现。
## 原理
[WWDC客户端](https://github.com/insidegui/WWDC)作者分享的一个开源项目[jonyfive](https://github.com/rlwimi/jonyfive)是把他WWDC项目中抓取字幕文件逻辑抽取出来，就是使用 swift 做的爬虫，可以结合项目做些有趣的东西。
这边我对该项目进行了一些修改，之前下载的直接就是 vtt 的文件，并没有对文本文件进行处理，我把vtt格式调整了正常阅读的模式，方便大家阅读。
> 不支持2108下载
## 安装
```
$ git clone https://github.com/Danny1451/WWDC-Subtitles.git
$ cd WWDC-Subtitles
$ swift build
```
## 抓取字幕
获取 2017 年 204 session 的字幕，保存到当前目录的 2017 文件夹
```
./wwwww subtitle -s 204 -v -y 2017
```
获取 2016 年 204 session 的 meta 信息，以 json 格式并且保存在当前目录的 sessions.json 文件中
```
./wwwww meta -s 204 -v -y 2016
```
## 如何下载中文

## 如何支持2018下载

[WWDC 2017 字幕抓取 & Guaka 介绍](http://danny-lau.com/2017/07/19/wwdc-2017-guaka/)

[WWDCHelper - 帮你更好下载 WWDC 中文字幕](https://www.jianshu.com/p/62a822835462)
