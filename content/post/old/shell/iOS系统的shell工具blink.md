---
title: iOS系统的shell工具blink
date: 2019-02-26 18:11:37
updated: 2019-02-26 18:29:38
comments: true
tags: [shell,iOS]
categories:
- 解决方案
keywords: 
permalink: 
---


### 简介
用于iOS的Blink Shell(为更多Shell util进行了编辑)
Blink是第一个利用`Mosh`和`SSH`支持的专业桌面级iOS终端。因此，我们可以明确地保证稳定的连接、闪电般的速度和完整的配置。它可以而且应该是你一整天的工具。
我们没有创建另一个终端来修复您的网站的运行。Blink从一开始就被打造为专业级产品。
我们从分析什么是必须拥有的开始，我们最终基于以下三个概念:
##### 快速呈现
Unix服务器中的dmesg应该是即时的。我们等不及要渲染了。我们不需要重新发明轮子来实现这一点。我们只是简单地使用Chromium的HTerm来确保渲染是完美和快速的，即使是那些特殊的、复杂的编^码。
##### 始终在线
Mosh超越了SSH的可变性。Mosh克服了我们都与移动连接相关的不稳定和间歇性连接。您可以检查Safari，而不必担心重新启动SSH连接。感谢Mosh，你可以完美地从家里跳到火车上，然后跳到办公室。Blink是坚如磐石的连接方式。Mosh很容易获得，并且可以很容易地安装在服务器上。访问https://mosh.org。
##### 完全可配置
Blink热情拥抱蓝牙连接键盘。一些人喜欢在Vim上设置Esc，另一些人喜欢在Emacs上设置Ctrl。瞬目是它们的冠军。但还有更多，因为我们想要更多。您还可以添加自己的自定义主题和字体来闪烁。在你的日常工作中，你处于自己的状态。
但是，眨眼更重要。请阅读:
你应该命令你的终端，而不是导航它。Blink会把你直接跳到一个友好的shell中，这样你就可以清楚地看到如何滚动了。
界面很简单。我们抛弃了所有的菜单，为您的终端打开了全屏。
使用滑动在打开的连接之间移动，滑下来关闭它们，甚至捏来缩放!
通过添加您自己的主机和RSA加密密钥来配置Blink连接。一切看起来都很熟悉，你很快就可以开始工作了!
### 使用
我们已经合并了SplitView，用于那些必要的谷歌搜索和与同事聊天。
更多信息，请访问[Blink Shell]()。
#### 添加新功能
这个分支还包含一组shell实用程序，因此您可以添加/删除文件、列出它们等等。
目前可用的命令有:
1. 操作命令
cd, setenv, ls, touch, cp, rm, ln, mv, mkdir, rmdir, df, du, chksum, chmod, chflags, chgrp, stat, readlink, compress, uncompress, gzip, gunzip，
2. 环境命令
pwd, env, printenv, date, uname, id, groups, whoami, uptime
cat,grep, wc
3. 访问命令
curl(包括http、https、scp、sftp…)、scp、sftp
4. tar
5. 第三方项目：使用外部项目:Python、Lua和TeX
您可以单独调用命令，或者使用python或lua使用script脚本。有重定向(“>”、“<”、“& >“…),但没有`pipe`工具。

所有这些命令都在`ios_system.framework`(预编译，用于工具)中。如果您想编辑源代码(以添加更多命令)，请参见:https://github.com/holzschu/ios_system。
#### 编辑脚本文件
我建议安装iVim (https://github.com/terrychou/iVim或https://itunes.apple.com/us/app/ivim/id1266544660?)mt=8)，使用ios11“edit-in-place”在Blink沙箱内编辑文件。
#### 密钥访问方式
curl可以打开与iPad之间的文件传输(ftp、http、scp、sftp…)。
1. 它使用了BLINKSHELL的密钥管理(即“config”创建密钥)。
2. 也可以指定键与路径:
{% codeblock  lang:swift %}
curl scp://host.name.edu/filename -o filename—key $SHARED/id_rsa—pass MyPassword
{% endcodeblock %}    
你也可以使用scp和sftp命令:

        scp user@host.name.edu:文件名
        sftp localFilename user@host.name.edu: ~ /

scp和sftp是通过curl实现的，通过重写参数来遵循curl语法。
利:更轻的实现，更小的内存成本，更不可能有函数名冲突。
弊:有些开关可能没有完全相同的含义。
#### 第三方语言包
语言包(Python、Lua和TeX)只提供等价的二进制文件。您可以使用包(/usr/local/texlive或/usr/lib/python2.7)传输目录，并将它们放到Blink应用程序的Library文件夹中。这是命令，如ls, rm, tar, mv…将是有用的。
注意:所有框架(除了curl)都是动态框架，以减少应用程序内存占用。
### 环境变量
在iOS中，由于沙箱限制，不能在**~**目录下写，只能在`~/Documents/`、`~/Library/`和`~/tmp`中写。大多数Unix程序假定配置文件位于`$HOME`中。因此，要么将`$HOME`重新定义为`~/Documents/`，要么将配置变量(使用setenv)设置为其他位置。
我用Blink在`MCPSession.m`文件。定义了以下变量:
```sh
setenv PATH = $PATH:~/Library/bin:~/Documents/bin
setenv PYTHONHOME = $HOME/Library/
setenv SSH_HOME = $HOME/Documents/
setenv CURL_HOME = $HOME/Documents/
setenv HGRCPATH = $HOME/Documents/.hgrc/
setenv SSL_CERT_FILE = $HOME/Documents/cacert.pem
```
如果你想永久地改变它们，最好是编辑`MCPSession.m`文件。
### 使用Blink
我们的UI非常直观，并优化了触摸设备的体验，这是非常重要的部分，terminal终端。您将直接跳到一个非常简单的shell中，因此您将知道如何操作。
这里还有一些技巧:
键入`help`在shell中查找信息。
新建shell命令：用两个手指轻敲创建一个新的shell。
移动shell命令：通过用手指移动两行shell命令。
新建连接/重连/退出：您可以退出会话并回到shell打开一个新连接。
关闭session：两个手指向下拖动来关闭会话。
缩放文本：使用缩放手势来增加或减少文本的大小。您也可以使用Cmd+或Cmd-如果使用键盘。
复制和粘贴：通过选择文本点击屏幕。
运行“config”来设置密钥。通过ssh-copy-id将它们安装到服务器。
在SmartKeys栏上的Ctrl和Alt修改器允许连续按下，就像在真正的键盘上一样。

### 支持python命令
[holzschu/blink](https://github.com/holzschu/blink.git)
[holzschu/pyhon_iOS](https://github.com/holzschu/pyhon_iOS.git)


### 配套使用的iVim工具
[iVim](https://github.com/holzschu/iVim.git)
readme介绍了，如何在真机上运行以及集成python环境。
