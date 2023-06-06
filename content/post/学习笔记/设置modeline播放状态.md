+++
title = "设置音频播放状态在modeline上的展示样式"
description = "博客简介"
date = 2023-06-06T19:28:00+08:00
tags = ["翻译"]
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

CLOSED: <span class="timestamp-wrapper"><span class="timestamp">&lt;2023-06-06 周二 08:10&gt;</span></span> 我们可以使用包'`emms-mode-line`'(由文件 `emms-mode-line.el` 提供)在 Emacs 模式行上显示当前播放曲目的信息。 

要激活此功能,请调用: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-mode-line)
(emms-mode-line 1)
```

也可以显示曲目播放了多长时间。此功能由文件 `emms-playing-time.el` 提供的 `emms-playing-time` 包定义。 

要使用此功能,请调用: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-playing-time)
(emms-playing-time 1)
```

Emms 播放时,可以在 modeline 上显示图形图标。此功能由 `emms-mode-line-icon.el` 提供。要启用,请调用以下内容并确保 `emms-mode-line-icon-enabled-p` 设置为非 nil 值: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require emms-mode-line-icon)
```

注意:'`(emms-playing-time -1)`'将完全禁用 `emms-playing-time` 模块,不推荐使用。(因为其他 emms 模块可能依赖于它) 

相反,要在模式行上切换显示播放时间,可以调用'`emms-playing-time-enable-display`'和'`emms-playing-time-disable-display`'。 

函数: `emms-playing-time-enable-display` 在模式行上显示播放时间。 

函数: `emms-playing-time-disable-display` 从模式行中删除播放时间。 

我们可以使用包'`emms-mode-line`'(由文件 `emms-mode-line.el` 提供)在 Emacs 模式行上显示当前播放曲目的信息。 

