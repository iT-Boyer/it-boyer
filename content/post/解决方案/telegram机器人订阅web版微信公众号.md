+++
title = "使用teleagram机器人实现公众号订阅"
description = "在RSSHub强大的额工具下，衍生了很多定制订阅服务的工具，针对微信公众号订阅基本上都被反爬了。尝试使用telegram实现公众号订阅"
date = 2021-10-21T18:11:00+08:00
publishDate = 2021-10-19T18:51:00+08:00
lastmod = 2021-10-21T18:11:08+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## I:辨别知识和信息 {#i-辨别知识和信息}

RSSHub口号是万物皆可订阅，可以爬去任何网站的信息，格式化为rss订阅格式。可以使用feedly，google 插件等工具实现订阅。  

现在知识平台大部分都集中在公众号上，微信的封闭模式，倒是爬去信息非常麻烦。在网上看了一些教程和RSSHub公布的订阅渠道都不能使用。  

为了解决这个问题，尝试通过telegram机器人实现和微信互通，让后转为RSSHub订阅的方式。  


## A1:激活/追问反思经验 {#a1-激活-追问反思经验}

RSS订阅技术对当前的学习环境并没有什么影响，但是想再emacs中实现集成阅读的化，只能通过elfeed工具订阅的体验方式才能达到沉浸体验。  

另一个原因是微信公众号订阅的渴望，尝试很多功能都无法实现公众号的订阅，就成了新病，想通过telegram机器人的方式实现。然后就有了这篇文章。  

可笑的elfeed并不支持订阅RSSHub的订阅源。  


## A2:内化应用知识，谱写愿景 {#a2-内化应用知识-谱写愿景}

真正的问题是解决：elfeed 订阅 RSSHub 无效的原因是什么，该怎么解决？  


## 要事第一，制定下一步行动 {#要事第一-制定下一步行动}


### <span class="org-todo done DONE">DONE</span> 如何实现elfeed 订阅RSSHub 订阅源。 {#如何实现elfeed-订阅rsshub-订阅源}


### ~~基于web端微信订阅微信公众号efb2w安装~~ {#}

（微信web端官方已不支持，经测试订阅方案无法使用）  

[为一个基于 efb v2 的公众号抓取输出方案提供支持](https://github.com/DIYgod/RSSHub/issues/2172)  

1.  docker 方式安装： [Docker镜像： lzyyauto/efb2w](https://hub.docker.com/r/lzyyauto/efb2w)
2.  python 本地安装： [安装并使用 EFB：在Telegram 收发微信消息](https://blog.1a23.com/2017/01/09/EFB-How-to-Send-and-Receive-Messages-from-WeChat-on-Telegram-zh-CN/)
