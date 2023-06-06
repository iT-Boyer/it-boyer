---
title: shell脚本路径和执行的路径区别
date: 2018-10-04 13:52:19
updated: 2018-10-04 13:52:19
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
## 获取shell脚本文件的绝对路径
basepath=$(cd `dirname $0`; pwd)
echo "sh脚本文件的绝对路径：$basepath"
## 当前执行脚本的路径
echo "当前执行脚本的路径也是cd路径打印方法:"`pwd`
echo "当前cd的目录名："`basename $(pwd)`
