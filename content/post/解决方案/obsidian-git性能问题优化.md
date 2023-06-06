+++ 
title = "obsidian-git性能问题优化"
description = "在手机端使用obsidian-git插件管理git版本遇到的几个个问题，总结以便参考借鉴"
date = "2022-10-20 19:27:19"
publishDate = "2022-10-20"
lastmod = "2022-10-20 19:27:19"
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

### 问题1
在配置远程路径时，使用ob-git命令面板编辑remote路径，无法生效。
解决办法：在pc端设置.git/config，指定远程路径位https协议，设置默认推送的origin，然后手动同步到手机端的obisian项目中，这时通过edit remote 命令查看远程列表，是https协议，就成功了。
### 问题2
卡顿问题优化，因为ob-git插件使用的js版本git命令，是轻量级的git版本，支持简单的push fetch一些简单命令，对文件数目的检索也有一定限制，导致在大项目中，运行消耗时间较长。

#### 清理子模块
这次优化主要从清理项目中多余的分支和子模块例如之间hugo主题，因为发布放在了github workflow完成，手机端不需要主题查看，于是清理了部分类似的子模块。

按道理来说子模块对ob-git不过敏有影响，为了瘦身做了相关操作。

#### clone最后一次提交版本
另一个瘦身方式，通过--depth=1，重新clone一份博客库，也可以起到瘦身作用。

