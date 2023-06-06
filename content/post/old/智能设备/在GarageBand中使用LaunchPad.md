---
title: 在GarageBand中使用LaunchPad
date: 2018-06-19 15:39:56
updated: 2018-09-22 21:19:01
comments: true
tags: [智能设备]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---

Garageband不支持控件表面，因此无法使用Launchpad/LaunchKey的全部功能。

在Garageband中，仅可以使用Launchpad来演奏虚拟乐器。
原因：`Faders`和`Knobs`将为输出MIDI CC消息([详细介绍](http://global.novationmusic.com/answerbase/what-midi-cc-messages-do-the-controls-on-the-launchkey-send))到支持手动操作的MIDI设备的插件。此外，打击垫还将发送固定的Note数据。
然而，Garageband本身没有传输、卷或Pan控件，不支持Launchpad的Volume or Pan Control。因此，只能在支持`HUI Protocol`的[DAW](http://us.novationmusic.com/sites/default/files/novation/downloads/10606/launchkey-mk2-daw-setup-guide.pdf)的DAW，或者选择`Ableton Live`，Launchpad才能发挥最大的作用。
[原文](https://support.novationmusic.com/hc/en-gb/articles/207556325-Can-I-use-my-Launchkey-in-Garageband-)
