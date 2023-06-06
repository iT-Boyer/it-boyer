---
title: RxSwift爬虫工具
date: 2018-08-28 12:03:26
updated: 2018-09-05 15:52:33
comments: true
tags: []
categories:
- 项目总结
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github it-boyer ReptileTool 60cac75 width = 30% %}
### 目的
1. 实践大话设计模式/swift基础语法/函数响应式开发

[正则表达式语法](https://msdn.microsoft.com/zh-cn/library/ae5bf541(VS.80).aspx)
[iOS开发之详解正则表达式](http://www.cocoachina.com/ios/20150415/11568.html)原文：NSRegularExpression Tutorial: Getting Started
[nshipster文章NSPredicate](http://nshipster.cn/nspredicate/)
[iOS中的谓词（NSPredicate）使用](http://www.cocoachina.com/ios/20160111/14926.html)

简短的定义：正则表达式提供了一种在指定文本文档中按指定模式进行搜索，并能基于匹配模式进行修改文本的一种方式。
正则表达式的通用用例：

1.  执行搜索：高亮显示搜索和替换
    1. UITextView的NSAttributedString属性来高亮显示搜索的结果
    2. 用text kit来实现高亮的功能
2. 验证用户输入

先来看看网络爬虫的基本原理：  
一个通用的网络爬虫的框架如图所示：  
![](082246341592742.png)

网络爬虫的基本工作流程如下：
1. 首先选取一部分精心挑选的种子URL；
2. 将这些URL放入待抓取URL队列；
3. 从待抓取URL队列中取出待抓取在URL，解析DNS，并且得到主机的ip，并将URL对应的网页下载下来，存储进已下载网页库中。此外，将这些URL放进已抓取URL队列。
4. 分析已抓取URL队列中的URL，分析其中的其他URL，并且将URL放入待抓取URL队列，从而进入下一个循环。
网络数据抓取

概念：网络数据抓取，也叫网络爬虫。是在我们iOS程序中，获取要抓取到的网页上的数据。  
用处：如果要用到某网站的一些数据，这个时候我们就要用到抓取数据技术。   
建议：建议抓取过程中，多利用分类，多写一些分类方法，有助于提高程序可读性，也可提高效率。  


今天先来介绍一下第一种：正则表达式
