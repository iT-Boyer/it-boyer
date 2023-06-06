---
title: Zsh插件之github使用
date: 2018-10-15 08:26:32
updated: 2018-10-15 08:26:32
comments: true
tags: [github,zsh]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---

##  github插件

This plugin supports working with GitHub from the command line. It provides a few things:

* Sets up the `hub` wrapper and completions for the `git` command if you have `hub` installed.
* Completion for the `github` Ruby gem.
* Convenience functions for working with repos and URLs.

###  Functions

* `empty_gh` - Creates a new empty repo (with a `README.md`) and pushes it to GitHub
* `new_gh` - Initializes an existing directory as a repo and pushes it to GitHub
* `exist_gh` - Takes an existing repo and pushes it to GitHub
* `git.io` - Shortens a URL using [git.io](https://git.io)
