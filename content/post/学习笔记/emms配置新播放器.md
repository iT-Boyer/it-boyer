+++
title = "在emacs中如何配置emms新播放器"
description = "博客简介"
date = 2023-06-06T16:51:00+08:00
draft = false
authors = ["iTBoyer"]
password = ""
+++

Emms 引入了一个用于播放音乐的高级抽象层,以便您可以根据需要对其进行定制。 

-   新建播放器	如何定义新的播放器。
-   简单的播放器 \`play'	使用 play 的播放器示例。
-   更复杂的播放器	使用 mpg321 的复杂播放器示例。


## 新建播放器 {#新建播放器}

文件 `emms-player-simple.el` 定义了一些简单的播放器作为开始,但为您最喜欢的播放器提供一个函数应该不难。我们将从一个简单的示例开始,演示如何在 Unix 下使用 play 命令播放我们的WAV文件。 


## 定制播放控制 “play” 命令 {#定制播放控制-play-命令}

Play 是一个非常简单的命令行播放器,支持各种格式。如果您希望您的 emms 播放WAV文件,只需在你的 .emacs 中放置以下行: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-player-simple)
(define-emms-simple-player play '(file) "\\.wav$" "play")
```

哈!这不是很简单吗? 

宏函数 `define-emms-simple-player` 至少需要三个参数。 

1.  第一个参数(在我们的示例中为 play)定义播放器的名称。它用于命名播放器函数。
2.  第二个是正则表达式,用于定义我们的播放器将播放的文件。 `\\.wav$` 匹配任何以点和字符串 wav 结尾的文件名。
3.  最后一个参数是我们实际用于播放我们文件的命令行命令。

您还可以添加路径,但我们只假设该命令在当前路径下执行。这三个参数之外的所有参数都是可选的。它们用于向您的参数添加要添加的命令行参数。 

如果您希望以最大可能的音量收听最喜欢的艺术家的WAV文件,请使用以下行: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-player-simple)
(define-emms-simple-player play
                           '(file)
                           "\\artist-*.wav$"
                           "play"
                           "--volume=100")
```

请注意,您必须将参数添加为字符串! 

用于 `define-emms-simple-player` 的命令行工具必须以一首歌作为参数,并在播放该特定歌曲后停止。对于任何其他概念,您将需要对 emms 进行更多定制... 


## 使用 define-emms-player 定制更复杂的播放器 {#使用-define-emms-player-定制更复杂的播放器}

如果简单的播放器能满足基本需求,那么您不需要阅读本章节。但是,如果您好奇如何在 emms 中使用(几乎)任何播放器,请继续阅读... 

在本章中,我们将使用mpg321构建一个可以暂停、重启并显示剩余时间的播放器。我们不会实现所有这些功能,但在本章结束后,您将知道如何定义它。 

命令 `define-emms-simple-player` 只是为 `define-emms-player` 提供一个抽象层,后者略微复杂但功能更强大! 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(define-emms-player "emms-mpg321-remote"
  :start 'emms-mpg321-remote-start
  :stop 'emms-mpg321-remote-stop
  :playablep 'emms-mpg321-remote-playable-p)
```

所以,这几乎就是全部！ 

`define-emms-player` 至少需要三个参数。第一个是 `播放器的名称` 。其余的是带函数调用的方法。 

需要三种方法:启动 `start` 、 `stop` 停止和 `playable` 可播放。 

1.  `start` 告诉 Emms 如何启动 track 曲目(原文如此!),
2.  `stop` 告诉 Emms 如何停止播放器,
3.  `playable` 返回非 nil,如果播放器可以播放曲目。

所以,我们只需要这三个函数来获得我们的 `mpg321-remote` : 

1.  首先,我们编写 `start` 函数我们将检查是否有打开的进程,如果没有,则启动一个进程。然后,我们将文件名和过滤器的字符串发送到进程。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun emms-mpg321-remote-start ()
         (unless (get-process ``mpg321-remote'')
           (setq emms-mpg321-remote-process
                 (start-process "mpg321-remote-process"
                                "*mpg321*" "mpg321" "-R" "abc"))
         (process-send-string "mpg321-remote-process"
                              (concat "l " (emms-track-name track)))
         (set-process-filter emms-mpg321-remote-process 'emms-mpg321-remote-filter)))
    ```
    我们需要过滤器,因为 `mpg321-remote` 在播放曲目后不会退出,就像简单的播放器一样。我们等待进程将输出 `“(at-sign)P 0”` (mpg321曲目结束的信号)发送到过滤器,然后调用 `emms-mpg321-remote-stop` 。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun emms-mpg321-remote-filter (process output)
         (when (string-match "(at-sign)P 0" output)
           (emms-mpg321-remote-stop)))
    ```

2.  编写 `emms-mpg321-remote-stop` 函数它只是测试是否有其他文件要播放,否则关闭进程。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun emms-mpg321-remote-stop ()
         (unless emms-playlist
           (process-send-string "mpg321-remote-process" "Q\n"))
    ```

3.  编写 `emms-mpg321-remote-playablep` 函数为了使其成为可播放的示例，其实我只是从 `emms-player-simple.el` 中窃取的。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun emms-mpg321-remote-playablep (track)
        "Return non-nil when we can play this track."
        (and (emms-track-file-p track)
    ```

现在,我们有了一个准备就绪的播放器,我们可以添加像 `emms-mpg321-remote-pause` 这样的命令。 

