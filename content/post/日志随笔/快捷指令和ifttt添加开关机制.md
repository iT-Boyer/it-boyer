+++
title = "通过 shortcut 集成 AI 和 Siri 实现语音对话"
description = "博客简介"
date = 2023-04-22T22:17:00+08:00
lastmod = 2023-04-22T22:19:25+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

在苹果手机上，通过 shortcut 集成 AI 和 Siri 实现语音对话，进一步提升了 AI 对话的体验。 

具体过程是，通过 Siri 唤醒 Shortcut 通过语音方式开启和 AI 对话，Shortcut 在接收到 AI 以文本形式反馈后，使用语音工具朗读文本。 

在朗读环节，体验有点差强人意，怎么优化 shortcut 语音为主题展开。 

在 Shortcuts 中,可以通过以下方式使用第三方的语音合成服务: 

1.  IFTTT - IFTTT 是一个流行的自动化平台,它提供了对各种 web 服务的连接,包括语音合成服务。你可以创建一个 IFTTT 应用连接一个语音服务,然后在 Shortcut 中使用"Run Web Request"操作调用该 IFTTT 应用来合成语音。
2.  API 直接调用 - 许多语音服务如 AWS Polly、Google Text to Speech、Baidu Text to Speech 都提供 API 可以直接调用。你可以在 Shortcut 中使用"Get Contents of URL"操作调用这些 API 来合成语音。
3.  HomeBridge 插件 - 如果你使用 HomeBridge,它有一些语音合成的插件,如 homebridge-polly-tts。你可以配置这些插件,然后在 Shortcut 中使用 HomeKit 操作来调用它们合成语音。

推荐的 Shortcut 和语音服务: 

1.  AWS Polly - 亚马逊的云端语音服务,它提供真人发音的神经语音,音质很高,通过 API 可以轻松调用。
2.  Google Text to Speech - 谷歌的免费语音合成服务,提供支持多种语言和发音人选择的高质量语音,也可以通过 API 或 IFTTT 轻松调用。
3.  百度语音合成 - 百度的语音服务,支持中文和英文,音质也不错,提供开放 API 可以在 Shortcut 中调用。
4.  IFTTT + Voicerss - Voicerss 是一款免费的 API 语音服务,IFTTT 应用可以连接它并在 Shortcut 中使用。
5.  homebridge-polly-tts - 如果你使用 HomeBridge,这个插件可以连接 AWS Polly 服务并通过 HomeKit 在 Shortcut 中调用。

这些都是比较流行可靠的语音服务与 Shortcut 集成方案,你可以按需选择并体验不同的语音效果。如果在使用过程中遇到任何问题,欢迎说明,我可以提供进一步的帮助。 


## 借助 HomeKit 优势 {#借助-homekit-优势}

homebridge-ifttt 是一个将 IFTTT 服务接入 HomeBridge 的插件。使用它,你可以在 IFTTT 创建连接各种语音服务的应用,然后在 HomeBridge 和 Shortcuts 中调用这些应用来进行语音合成。要安装并使用 homebridge-ifttt,可以按以下步骤操作: 

1.  安装 HomeBridge - 在一台安装有 Node.js 的设备上安装 HomeBridge。
2.  注册 IFTTT 帐户 - 前往 ifttt.com 并注册一个免费 IFTTT 帐户。
3.  安装 homebridge-ifttt - 运行命令:

npm install -g homebridge-ifttt 

1.  在 HomeBridge 中配置 IFTTT 配件:

<!--listend-->

```json { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
"platforms": [{
    "platform": "IFTTT",
    "makerkey": "your_maker_key",
    "device": {
        "name": "IFTTT"
    }
}]
```

将 your_maker_key 替换为你的 IFTTT Maker Key。 

1.  在 IFTTT 中创建连接语音服务的应用:
2.  选择"IFTTT 访问授权"服务作为触发器
3.  选择需要的语音服务如"Voicerss - 文本转语音"作为操作
4.  当 IFTTT 接收到来自 IFTTT Maker Channel 的触发时,Voicerss 将合成并播放语音
5.  重启 HomeBridge,IFTTT 应用和语音服务会出现在 Home 应用中。
6.  在 Shortcuts 中,使用 HomeKit 操作"播放语音(使用 IFTTT)"来调用 IFTTT 应用,语音服务将进行语音合成。

可选的语音服务: 

-   Voicerss: 免费语音 API 服务,IFTTT 应用提供,中文和英文
-   Anthropic:免费语音 API 服务,IFTTT 应用提供,多语言
-   Other TTS Services:基于软件的 TTS 服务,IFTTT 应用提供,英文等

这种方案可以连接你选择的多种语音服务。如果在配置或使用过程中有任何问题,欢迎说明,我将提供详细的帮助。也欢迎分享使用 HomeBridge 和 IFTTT 进行语音集成的体验与建议。将有助于帮助更多 Shortcut 用户发掘并享受它强大的语音化功能! 

```shell { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
curl https://maker.ifttt.com/trigger/totts/with/key/dq0HRmkxprvckuzMcgVFgu?value1=你好

curl -X POST -H "Content-Type: application/json" -d '{"value1":"你好"}' https://maker.ifttt.com/trigger/{totts}/json/with/key/dq0HRmkxprvckuzMcgVFgu
```

