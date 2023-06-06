---
title: git-log的使用
date: 2018-09-05 15:52:33
updated: 2018-09-05 15:52:33
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
## trello 卡片
<blockquote class="trello-card"><a href="https://trello.com/c/RWMUfhlD/4-git-log%E4%BD%BF%E7%94%A8">git log使用</a></blockquote><script src="https://p.trellocdn.com/embed.min.js"></script>

[查看提交历史](http://iissnan.com/progit/html/zh/ch2_3.html)
来看一个实际的例子，如果要查看 Git 仓库中，2008 年 10 月期间，Junio Hamano 提交的但未合并的测试脚本（位于项目的 t/ 目录下的文件），可以用下面的查询命令：
```bash
$ git log --pretty="%h - %s" --author=gitster --since="2008-10-01" \
--before="2008-11-01" --no-merges -- t/
5610e3b - Fix testcase failure when extended attribute
acd3b9e - Enhance hold_lock_file_for_{update,append}()
f563754 - demonstrate breakage of detached checkout wi
d1a43f2 - reset --hard/read-tree --reset -u: remove un
51a94af - Fix "checkout --track -b newbranch" on detac
b0ad11e - pull: allow "git pull origin $something:$cur
```
Git 项目有 20,000 多条提交，但我们给出搜索选项后，仅列出了其中满足条件的 6 条。
## gitk图形工具
![](http://iissnan.com/progit/book_src/figures/18333fig0202-tn.png)
