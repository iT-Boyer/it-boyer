---
title: swift支持的正则（textKit）
date: 2018-09-05 15:52:33
updated: 2018-09-05 15:52:33
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---

<blockquote class="trello-card"><a href="https://trello.com/c/kSIkFQXO/5-swift%E6%94%AF%E6%8C%81%E7%9A%84%E6%AD%A3%E5%88%99%EF%BC%88textkit%EF%BC%89">swift支持的正则（textKit）</a></blockquote><script src="https://p.trellocdn.com/embed.min.js"></script>

[正则表达式语法](https://msdn.microsoft.com/zh-cn/library/ae5bf541(VS.80).aspx)
[iOS开发之详解正则表达式](http://www.cocoachina.com/ios/20150415/11568.html)原文：NSRegularExpression Tutorial: Getting Started
[nshipster文章NSPredicate](http://nshipster.cn/nspredicate/)
[iOS中的谓词（NSPredicate）使用](http://www.cocoachina.com/ios/20160111/14926.html)
## 谓词和正则表达式的区别及适用场景
1. 谓词的对象可以是字符串，集合，同时支持sql语法和正则表达式
2. 正则表达式，对字符串

## 正则表达式
简短的定义：正则表达式提供了一种在指定文本文档中按指定模式进行搜索，并能基于匹配模式进行修改文本的一种方式。
正则表达式的通用用例：

1.  执行搜索：高亮显示搜索和替换
    1. UITextView的NSAttributedString属性来高亮显示搜索的结果
    2. 用text kit来实现高亮的功能
2. 验证用户输入
