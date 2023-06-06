---
title: vim一般模式下查找和替换命令
date: 2018-10-04 23:48:08
updated: 2018-10-04 23:48:08
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
###  `/`,`?`查找命令
* `/word` 向下查找word字符串
* `?word`向上查找word字符串
组合查找
`n`:  代表重复前一个查找的操作
`N`:反向进行前一个查找操作  
###  `s/old/new/g`替换命令

1. 行间查找 
在第n1和n2行查找`word1`比替换为`word2`
```
:n1,n2s/word1/word2/g
```
举例：  `：100,200s/vbird/VBIRD/g`

2. 全文查找并替换
```
:1,$s/word1/word2/g
```
从第一行到最后一行查找字符串word1字符串，并将字符串word1替换为word2

3. 用户确认替换提示
```
:1,$s/word1/word2/gc
```
从第一行到最后一行查找字符串word1，并将字符串word1替换为word2,在替换之前提示用户确认是否替换
