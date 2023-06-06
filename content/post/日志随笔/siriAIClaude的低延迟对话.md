+++
title = "siri 对话 Claude AI，低延迟畅聊"
description = "博客简介"
date = 2023-04-22T23:26:00+08:00
lastmod = 2023-04-23T08:03:26+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

在体验 AI 对工作和生活越来越大，疯狂的探索更多的技巧和使用场景，改善生活习惯。很自然，就瞄上了 Siri 本该强大的小助手，是时候通过 AI 来提升对话体验了。 

这段时间，在网上出现很多解决方案和工具，把 chatgpt 集成到 siri ，都离不开 Shortcuts 工具。 

在 shortcut 中可以使用手机系统的朗读功能，把 AI 的回答，用系统自带的人声朗读。在体验上不太好，尝试借助第三方语音合成服务 API 把 AI 文本转为更多人声，然后回传给手机播放，达到定制的效果。 

在短视频中很多使用 Azure 语音配音的，效果很好，在国内想使用它，很难获取体验资格，拿不到 API 密钥。在申请 visa 卡，注册帐号，消耗了太多精力，尝试了翻墙注册新帐号，用信用卡生成器，美国地址生成器，仍无法认证，最后只能放弃。 

其实，在朗读文本时，不满意 Shortcut 的朗读效果的话，还可以尝试在用  Siri 激活捷径的时候，使用 Siri 来识别文本，可以很流畅朗诵，体验还是蛮不错。 

现在回到正题，在网上有很多现成的 shortcut 快捷指令，已经实现了 Siri + chatgpt 的效果。但在适用过程中，chatgpt API 延迟是硬伤，频繁被 Siri 误判为链接异常，稍候再试，中断了对话。 

这段时间一直在用 slack 中的 Claude 解决很多技术问题，能够提供很多高质量的回答，好感度满分。 

在 Slack 有一些使用经验，了解过它强大扩展特性，提供 Webhook ， Web Api ，Socket mode 等，帮助第三方平台能够很好的接入它功能。基于扩展的特性，现在很多开源的 chatgpt 插件会接入 Slack 对接，作为机器人，提升 AI 聊天的体验。 

在体验过 mygptReader ， be-on-anything 一些很好的把 chatgpt 集成到了 Slack 中，体验效果非常好，但是 mygptreader 限制太多， be-on-anything 终究要依赖本地服务。 

在使用使用了这么多好用的工具插件之后，该怎么使用 Shortcut 实现 Siri AI 的想法，就有了一个能解决网络延迟，免费和 AI 聊天的方案。反其道而行之：在 Shortcut 使用获取网址操作命令，借助 Slack 的开放性，实现 Siri 调用 API 和 Claude 对话。 

思路：将 Slack 作为平台，Claude APP 聊天作为 AI 服务。通过Income webhook, Slack Web API ，Slack App 等功能，定义一个轮询指令，用来监听 Claude 的最后一条消息。这样拿到聊天历史的 JSON ，解析出 Claude 的回答的内容，就能让 Siri 帮你朗读出来，实现 Siri + AI 低延迟的体验效果。 

在shortcut 实现 Siri 和 Claude 聊天，主要用到这个APi 

向Claude AI 机器人发送消息的接口：[chat.postMessage method | Slack](https://api.slack.com/methods/chat.postMessage) 

轮询指令监听 AI 获取最新一条聊天记录：[conversations.history method | Slack](https://api.slack.com/methods/conversations.history/code) 

文档中提供了接口验证工具，很大的便利开发效率。 

