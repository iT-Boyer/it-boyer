+++
title = "emacs常用快捷键"
date = 2019-08-20T23:25:00+08:00
lastmod = 2019-08-21T14:47:42+08:00
tags = ["doom"]
categories=["博客站务"]
draft = false
author = "iTBoyer"
#password = 1234
+++

## 统计命令 {#统计命令}

org-insert-columns-dblock C-c C-x i 对任务列表生成列表视图.C-c C-x C-c 生成临时的任务列表
org-clock-in  C-x C-x C-i 统计实际任务执行时间
org-clock-out C-c C-x C-o 统计任务结束的总时长
org-clock-report C-c C-x C-r 统计光标的任务时间报告


## 插入命令 {#插入命令}

插入代码片段:
C-c C-q 标签命令
C-c i s 插入脚本
, i h 插入同级节点
C-c C-w org文件之间迁移节点


## 目录命令 {#目录命令}

SPC f j + 创建目录
ivy搜索：C-s 调用swiper
C-c C-c: 执行命令,光标必须定位在BEGIN\_SRC模块


## yasnippet使用 {#yasnippet使用}

暂时把所有的snippet都移动到org-mode中，可以正常加载使用

{{< highlight nil >}}
mv plantuml-mode org-mode
{{< /highlight >}}

仅支持在org文件内，使用`Tab`展开。


## magit版本控制命令 {#magit版本控制命令}

spc g s 进入status窗口
   ? 进入git帮助窗口
   c commit输入提交信息
   C-c C-c 完成提交
   C-c C-k 取消提交操作

<span class="timestamp-wrapper"><span class="timestamp">[2019-08-19 一 17:07]</span></span>
