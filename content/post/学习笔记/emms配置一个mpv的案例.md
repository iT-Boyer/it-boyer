+++
title = "通过 emms 定制 mpv 播放器的案例"
description = "博客简介"
date = 2023-06-07T13:50:00+08:00
tags = ["翻译"]
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

以下内容包含来自 Emms 实战配置的示例,展示了人们对 Emms 默认设置所做的各种各样广泛的修改。 

下面的摘录包括:通过 dbus 集成,为 `See The Browser` 定义一个 “recent” 的过滤器,通过 `emms-history.el` 实现持久播放列表,并通过 `emms-librefm-stream.el` 发送音轨信息: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; notifications
(require 'emms-dbus)
(emms-dbus-enable)
;; covers
(setq emms-browser-covers #'emms-browser-cache-thumbnail-async)
(setq emms-browser-thumbnail-small-size 64)
(setq emms-browser-thumbnail-medium-size 128)
;; filters
(emms-browser-make-filter "all" #'ignore)
(emms-browser-make-filter "recent"
                          (lambda (track) (< 30
                                             (time-to-number-of-days
                                              (time-subtract (current-time)
                                                             (emms-info-track-file-mtime track))))))
(emms-browser-set-filter (assoc "all" emms-browser-filters))
;; history
(emms-history-load)
;; libre-fm
(emms-librefm-scrobbler-enable)
```

在以下内容中,可以看到有关保存播放列表、播放列表交互以及向特定播放器后端添加特殊参数的一些默认设置。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq-default
   emms-source-file-default-directory "/mnt/db/mediaCore/sound_music/"

   emms-source-playlist-default-format 'm3u
   emms-playlist-mode-center-when-go t
   emms-playlist-default-major-mode 'emms-playlist-mode
   emms-show-format "NP: %s"

   emms-player-list '(emms-player-mpv)
   emms-player-mpv-environment '("PULSE_PROP_media.role=music")  ;; 1
   emms-player-mpv-parameters '("--quiet" "--really-quiet"       ;; 2
                                "--no-audio-display"
                                "--force-window=no"
                                "--vo=null"))
```

在设置 EMMS 使用 MPV 播放器时,可以通过 `emms-player-mpv-environment` 变量为 MPV 设置环境变量。 

例如,可以设置环境变量 `PULSE_PROP_media.role=music`,将 MPV 播放的音频流设置为 `music` 角色。这样当 MPV 播放音频时,PulseAudio 会将其识别为音乐,应用音乐 DSP 等效果。 

可以在配置文件中加入以下代码: `(setq emms-player-mpv-environment '("PULSE_PROP_media.role=music"))` 

当 EMMS 调用 MPV 播放音频或视频时,会为 MPV 设置这个环境变量。MPV  Inherits 这个环境变量,PulseAudio 获取到设置,将 MPV 的音频流识别为音乐。 

这使得我们可以更深入地定制 MPV 的行为。除了将音频流设置为 `music` 外,还可以: 

-   设置视频画面颜色属性等。
-   选择视频输出方式,如 `vo=opengl` 等。
-   其他 MPV 支持的任何选项。

`emms-player-mpv-parameters` 变量可以为 EMMS 调用的 MPV 播放器设置命令行参数。 

这会让 MPV 具有以下行为: 

1.  `--quiet` 和 `--really-quiet`: MPV 不输出任何信息,静默运行。

2.  `--no-audio-display`: MPV 不显示音频波形等视觉化信息。

3.  `--force-window=no`: MPV 不创建自己的窗口,在后台运行。

4.  `--vo=null`: MPV 不进行任何视频输出,只有音频输出。

所以通过设置这些参数,我们让 MPV 以静默后台模式运行,没有任何视频和最小的音频输出(只在后端通过 PulseAudio 播放音频)。 

这种设置非常适合只播放音乐,让 EMMS 完全控制 MPV,不在桌面环境中创建任何视频或音频窗口,不产生任何干扰,获得最纯净的音乐播放体验。 

当然,我们也可以根据需要设置其他 MPV 支持的任意命令行参数。EMMS 通过调用命令启动 MPV,所以整个 MPV 的功能和选项,都可以通过 `emms-player-mpv-parameters` 变量设置的命令行参数提供给 EMMS 使用。 

