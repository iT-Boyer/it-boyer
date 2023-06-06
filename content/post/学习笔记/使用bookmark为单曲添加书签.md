+++
title = "使用 Bookmarks 工具为单曲添加书签"
description = "博客简介"
date = 2023-06-06T19:15:00+08:00
tags = ["翻译"]
categories = ["学习笔记"]
draft = true
authors = ["iTBoyer"]
password = ""
+++

Emms 可以通过 `emms-bookmarks` 在媒体文件中保存 “暂时书签”。文件 `emms-bookmarks.el` 提供 `emms-bookmarks` 包。 

在播放某些媒体时,调用 `M-x emms-bookmarks-add` 会首先暂停播放,然后提示输入描述书签的名称。曲目可以关联多个书签。 

要跳转到当前曲目的下一书签和上一书签,请分别调用 `M-x emms-bookmarks-next` 和 `M-x emms-bookmarks-prev` 。 

要清除当前曲目的所有书签,请调用 `M-x emms-bookmarks-clear` 。 

