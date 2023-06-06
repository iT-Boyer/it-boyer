---
layout: post
title: "git svn桥接命令的基础"
date: 2016-01-05 14:18:00 +0800
comments: true
tags: [git]
categories:
- 解决方案
---


[git svn](http://git-scm.com/book/zh/v1/Git-与其他系统-Git-与-Subversion#git-svn)

通过几个简单的工作流程了解到`git svn`常见命令：
值得警戒的是，在使用 git svn 的时候，你实际是在与 Subversion 交互，Git 比它要高级复杂的多。尽管可以在本地随意的进行分支和合并，最好还是通过衍合保持线性的提交历史，
1. 尽量避免类似与远程 Git 仓库动态交互这样的操作。
2. 避免修改历史再重新推送的做法，也不要同时推送到并行的 Git 仓库来试图与其他 Git 用户合作。
3. Subersion 只能保存单一的线性提交历史，一不小心就会被搞糊涂。
4. 合作团队中同时有人用 SVN 和 Git，一定要确保所有人都使用 SVN 服务来协作——这会让生活轻松很多。

