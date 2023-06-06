+++
title = "30 GNU FM"
description = "博客简介"
date = 2023-06-06T18:59:00+08:00
tags = ["翻译"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

GNU FM是一个用于运行音乐社区网站的自由软件。它是为音乐社区网站 `Libre.fm` 创建的。 

Emms 可以分别使用 `emms-librefm-scrobbler.el` 和 `emms-librefm-stream.el` 向GNU FM服务器发送曲目信息和流式音乐。 

Emms 默认配置为使用 `Libre.fm`,但可以通过配置变量 `emms-librefm-scrobbler-handshake-url` 为GNU FM服务器的URL来与任何GNU FM服务器一起使用。 

向GNU FM服务器提供凭据的推荐方式是使用 authinfo 文件。在 `auth-info` 文件(通常是 `~/.authinfo.gpg` )中添加验证,如: 

```text
machine libre.fm login USERNAME password PASSWORD
```

如果您使用的是 `libre.fm` 以外的其他服务器,请将 `libre.fm` 更改为匹配 `emms-librefm-scrobbler-handshake-url` 。 

另外,您可以通过设置这些变量在 `init-file` 中以纯文本形式保存密码: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq emms-librefm-scrobbler-username "USERNAME"
      emms-librefm-scrobbler-password "PASSWORD")
```

-   上传曲目信息	   如何提交已听曲目信息。
-   GNU FM流音乐    从GNU FM服务器流式音乐。


## 30.1 Uploading Track Information {#30-dot-1-uploading-track-information}

GNU FM服务器,如 `Libre.fm` 可以选择使用 Emms 发送到网站服务器的信息来存储用户的听力习惯。利用用户听力习惯的记录,网站旨在通过分析用户的音乐品味来为用户推荐音乐。 

使用以下内容将此功能加载到 Emms 中: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-librefm-scrobbler)
```

此功能也可以通过查看“emms-all”设置级别中的设置来启用。 

使用 `emms-librefm-scrobbler-enable` 启用上传 Emms 播放的曲目详细信息到GNU FM服务器。曲目的详细信息将在曲目播放结束时上传到服务器。您可以使用 `emms-librefm-scrobbler-disable` 禁用此行为。 


## 30.2 GNU FM Streaming {#30-dot-2-gnu-fm-streaming}

如果GNU FM服务器提供流媒体音乐服务,您可以通过加载以下内容来利用它: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-librefm-stream)
```

此功能也可以通过查看“emms-all”设置级别中的设置来启用。 

然后调用 `emms-librefm-stream` 并输入您要听的电台的URL,例如“librefm://globaltags/Classical”。 


## 31 D-Bus {#31-d-bus}

Emms 可以提供 MPRIS 接口,允许通过 D-Bus 进行控制。 

要启用此功能,首先加载: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(require 'emms-mpris)
```

然后使用 `emms-mpris-enable` 打开它。您可以使用 `emms-mpris-disable` 关闭它。 

目前,Emms 对MPRIS规范的实现不完整:当前不支持更改音量。 

