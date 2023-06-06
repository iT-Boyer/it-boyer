+++
title = "使用 shortcuts 订阅 Claude AI对话"
description = "博客简介"
date = 2023-04-22T22:28:00+08:00
lastmod = 2023-04-22T22:29:16+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## 第一步搞定 slack webhooks 配置 {#第一步搞定-slack-webhooks-配置}

Slack 中 Incoming Webhooks 是输入消息的功能，可以作为订阅工具介绍用户的消息吗？ 

Slack 的 Incoming Webhooks 功能允许外部应用程序通过 HTTP 请求向 Slack 发送消息。它不能直接用于订阅用户消息。但通过 webhook 消息中的用户信息,我们可以实现某个用户消息的过滤与订阅。具体步骤如下: 

1.  在 Slack 中,选择要接收消息的频道,点击“应用与集成”。选择“传入 Webhook”,点击“添加配置”。
2.  这会生成一个 webhook URL。外部应用可以通过向此 URL 发送 HTTP POST 请求来向该频道发送消息。
3.  收到的 webhook 请求中的 body  Data 包含了构建 Slack 消息所需的所有信息,包括: 
    -   text:消息文本内容
    -   username:发送者用户名
    -   channel:消息所在频道 ID
    -   user:发送用户的 ID
    -   等等
4.  外部应用接收到该 webhook 请求后,可以解析请求信息,提取 用户 ID 与用户名等信息。
5.  通过用户 ID 对消息进行过滤,只处理目标用户发送的消息。然后提取 text 字段的文本内容,并在应用界面进行显示,实现该用户消息的订阅与接收。
6.  Slack 应用配置 webhook 成功后,外部应用需要定期轮询 webhook URL,等待接收 Slack 发送过来的 webhook 请求,然后进行解析与处理。
7.  这样,通过解析webhook 请求与过滤用户信息,外部应用就可以实现订阅 Slack 中某个用户的消息。

Incoming Webhooks 本身是让外部应用可以推送消息到 Slack 的功能,但借助于 webhook 请求中的用户信息,我们可以 newspaper subscription slack 的用户消息。它需要外部应用保存用户信息与定期检索 webhook URL,属于一种比较hack 的方式。 

Slack 目前官方提供的订阅消息接口有两个: 1.rtm.connect - 该接口建立一个 WebSocket 链接,通过该链接实时接收频道消息。这是目前 Slack 推荐的消息订阅方式。使用方式: 

-   调用 rtm.connect 获取 WebSocket URL
-   启用 WebSocket 客户端与该 URL 建立链接
-   通过 WebSocket 接收实时消息数据

2.channels.history - 该接口用于获取频道历史消息。通过定期调用,可以实现消息订阅。使用方式: 

-   调用 channels.history 获取频道最近 N 条消息
-   设置定期定时器,每 T 秒调用一次 channels.history
-   比较历史消息与新获取的消息,提取新增消息
-   处理新增消息并作出响应

相比 chat.postMessage 的轮询方式,rtm.connect 的 WebSocket 订阅和 channels.history 的定期拉取都是更优雅高效的消息订阅方案。我强烈建议采用其中一种方式实现 Slack 消息订阅。 


## 第一步在 shortcuts 中订阅 webhook URL {#第一步在-shortcuts-中订阅-webhook-url}

iOS Shortcuts 应用中没有直接的“接收 Webhook”操作。但我们可以通过其他操作与功能实现 webhook 消息的接收与处理。 

以下是具体实现步骤与截图: 

1.  添加“获取网址”操作,输入之前在 Slack 中获取的 webhook URL。这将返回该 URL 的内容,也就是发送过来的 webhook 消息。
2.  添加一个 IF 条件判断,判断“获取网址”操作的输出是否包含“webhook 消息”字段。如果包含,则表示接收到新的 webhook 消息。
3.  如果接收到新消息,我们提取消息中的“text”字段作为 Claude 应用回复的内容。
4.  最后,添加一个“显示通知”操作,在标题中选择“Claude 回复”,内容选择前面提取的文本内容变量。这将在 iOS 中显示回复通知。
5.  保存快捷方式后,它就会定期轮询 webhook URL,等待接收到新回复消息并显示通知。


## 第三步实现 shortcuts 轮询订阅 {#第三步实现-shortcuts-轮询订阅}

在 iOS Shortcuts 中,可以通过添加定时器与循环操作实现对 webhook URL 的轮询订阅。具体步骤如下: 

1.  添加“获取网址”操作,输入 Slack 中生成的 webhook URL。这将返回 URL 内容,也就是 Claude 应用发送过来的 webhook 消息。
2.  添加一个“重复”循环操作。在“重复次数”字段输入一个较大的值,例如 100 次。这将创建一个循环,重复 100 次获取 webhook 消息。
3.  在循环中继续添加“如果”条件判断。判断“获取网址”操作的输出是否包含“webhook 消息”字段。如果包含,表示接收到 Claude 应用的新回复,跳出循环。
4.  如果接收到新回复,提取消息中的文本内容并显示通知。之后添加一个“停止运行”操作,跳出整个快捷方式流程。
5.  如果 100 次循环后仍未接收到新回复,快捷方式将自动停止运行。此时,我们需要添加一个“延迟”操作,例如 2 分钟。然后使用“继续运行”操作重新启动该快捷方式,再进入循环监听 webhook URL。
6.  这样,快捷方式就实现了定期轮询订阅 webhook URL,等待接收 Claude 应用的新回复。一旦收到新回复,显示通知并停止运行。如果超时未收到回复,就延迟指定时间后重新启动监听。

具体流程如下: 获取网址(Webhook URL)→ 重复(100)→ 如果(包含“webhook 消息”字段)→提取文本内容→ 显示通知→ 停止运行→ 延迟(2 分钟)→ 继续运行→ 获取网址(Webhook URL)→ 重复(100)... 

