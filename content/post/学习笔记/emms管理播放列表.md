+++
title = "使用EMMS管理多媒体播放列表"
description = "博客简介"
date = 2023-06-06T19:05:00+08:00
tags = ["翻译"]
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

Emms 可以有多个播放列表,因为播放列表只是带有曲目列表的其他缓冲区。 

您可以使用文件 `emms-metaplaylist-mode.el` 提供的'`emms-metaplaylist-mode`'管理多个播放列表。 

使用 `M-x emms-metaplaylist-mode-go` 启动播放列表管理器。播放列表管理器将列出播放列表并标记当前播放列表。 

可用的命令如下: 

`RET`  使光标处的缓冲区成为 Emms 播放列表缓冲区并切换到它。 

`SPC`  使光标处的缓冲区成为 Emms 播放列表缓冲区(但不切换到它)。 

`n`   将光标移到下一个播放列表。 

`p`  将光标移到上一个播放列表。 

`g`  更新播放列表管理器缓冲区。 

`C`  创建一个新的 Emms 播放列表缓冲区。 

`C-k`  杀死光标处的 Emms 播放列表缓冲区。 

`c`  将光标移到当前播放列表缓冲区。 

`q`  杀死播放列表管理器。 

