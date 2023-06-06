---
title: 使用Sphinx制作简洁而又美观的文档
date: 2018-11-07 12:27:56
updated: 2018-11-07 20:45:31
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
{% github it-boyer  width = 30% %}

[使用 sphinx 制作简洁而又美观的文档](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/)
### 安装
```
$ easy_install sphinx
Searching for sphinx
Reading http://pypi.python.org/simple/sphinx/
Reading http://sphinx.pocoo.org/
Best match: Sphinx 1.0.5
Downloading http://pypi.python.org/packages/[...]
Processing Sphinx-1.0.5-py2.5.egg
[...]
Finished processing dependencies for sphinx
```
### 创建工程
```
$ sphinx-quickstart 
Welcome to the Sphinx 1.0.5 quickstart utility.

Please enter values for the following settings (just press Enter to
accept a default value, if one is given in brackets).
[...]
```
工作目录的列表
```
├── Makefile
├── _build
├── _static
├── conf.py
└── index.rst
```
让我们详细研究一下每个文件。
* **Makefile**：编译过代码的开发人员应该非常熟悉这个文件，如果不熟悉，那么可以将它看作是一个包含指令的文件，在使用 make 命令时，可以使用这些指令来构建文档输出。
* **_build**：这是触发特定输出后用来存放所生成的文件的目录。
* **_static**：所有不属于源代码（如图像）一部分的文件均存放于此处，稍后会在构建目录中将它们链接在一起。
* **conf.py**：这是一个 Python 文件，用于存放 Sphinx 的配置值，包括在终端执行 sphinx-quickstart 时选中的那些值。
* **index.rst**：文档项目的 root 目录。如果将文档划分为其他文件，该目录会连接这些文件。
### make生成文档
以[py-trello](https://github.com/sarumont/py-trello)为例子
```
$ git clone py-trello
$ cd py-trello/docs
$ make html
>
sphinx-build -b html -d _build/doctrees   . _build/html
正在运行的是 Sphinx v1.8.1
创建输出目录…
```

