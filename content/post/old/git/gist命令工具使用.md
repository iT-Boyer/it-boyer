---
title: gist命令工具使用
date: 2018-10-11 17:17:52
updated: 2018-10-11 17:58:15
comments: true
tags: [git]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github defunkt gist 8d86604 width = 30% %}
[按照gist](http://macappstore.org/gist/)
### 安装gist
```
brew install gist
```
### 创建gist
上传`a.rb`中的代码段：
```
gist a.rb
```
上传多个文件`a.rb`,`b.rb`,`c.rb`的代码段:
```
gist a b c
gist *.rb
```
作为文件上传：获取`STDIN`数据，并使用`-f`指定文件名`test.rb`上传：
```
gist -f test.rb <a.rb
```
直接从剪切板上传文本:
```
gist -P
```
设置隐私片段 `-p`:
```
gist -p a.rb
```
添加片段描述信息`-d`:
```
gist -d "Random rbx bug" a.rb
```
更新现有的片段 `-u`:
```
gist -u GIST_ID FILE_NAME
例子：
gist -u 42f2c239d2eb57299408 test.txt
```
‌If you'd like to copy the resulting URL to your clipboard, use `-c`.
```
gist -c <a.rb
```
‌If you'd like to copy the resulting embeddable URL to your clipboard, use `-e`.
```
gist -e <a.rb
```
打开浏览器访问片段 `-o`.
```
gist -o <a.rb
```
‌To list (public gists or all gists for authed user) gists for user
`gist -l` : all gists for authed user
`gist -l defunkt` : list defunkt's public gists
To read a gist and print it to STDOUT
```
gist -r GIST_ID
例子：
gist -r 374130
````
‌See `gist --help` for more detail.

#### 使用gist命令


#### 使用gistit快捷工具
{% github jrbasso gistit 99fc659 width = 30% %}
1. [安装](http://macappstore.org/gistit/)
```
brew install gistit
```
2. [使用说明](http://gistit.herokuapp.com)
```
# Creating a public gist from other app response
ls | gistit

# Creating a private gist from other app response
ls | gistit -priv

# Specifying the gist filename
ls | gistit -i list.txt

# Sending files
gistit file.txt

# Sending multiple files in a private gist
gistit -priv file1.txt file2.c

# Setting gist description
gistit -d "This is just a sample" sample.txt

# Setting gist description, private and with multiple files
gistit -d "Sample" -priv file1.txt file2.txt file3.txt

# Help
gistit -h

# Version
gistit -v
```
