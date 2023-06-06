+++
title = "使用swift开发telegram机器人方案"
description = "机器人执行预设命令，简化繁琐的操作流程"
date = 2021-09-09T16:19:00+08:00
publishDate = 2021-09-09T15:53:00+08:00
lastmod = 2021-09-09T16:19:32+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [启发](#启发)
- [开发bot 机器人](#开发bot-机器人)

</div>
<!--endtoc-->


## 启发 {#启发}

在了解现金流游戏的过程，意外发现记账工具costflow 结合telegram 机器人，实现记账功能的方法。  

所以，萌生了通过swift 方式，开发个人机器人的想法，就有了这篇样例文章。  

先说下结果，swift 开发流程已经调通，本文仅介绍到swift 环境配置过程遇到的问题。在最后联调机器人时，出现了timeout 错误，还没有解决：  

{{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
endpoint: getMe, data:
Unable to fetch bot information: Libcurl error 28: Timeout was reached
{{< /highlight >}}

主要参考的文章：  

[Bots: An introduction for developers](https://core.telegram.org/bots#6-botfather)  

[GitHub - rapierorg/telegram-bot-swift: Telegram Bot SDK for Swift (unofficial)](https://github.com/rapierorg/telegram-bot-swift)  

[使用 Telegram Bot + Beancount 记账 | Ahonn&#x27;s Blog](https://www.ahonn.me/blog/use-telegram-bot-and-beancount-for-accounting)  


## 开发bot 机器人 {#开发bot-机器人}

主要基于 Xcode13 介绍 swift package manager 创造项目，并集成 `TelegramBotSDK` 库  

{{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
mkdir hello-bot
cd hello-bot
swift package init --type executable
{{< /highlight >}}

配置依赖：  

{{< highlight swift "linenos=table, linenostart=1, hl_lines=29-29 0-0" >}}
// swift-tools-version:5.5
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "todo-bot",
    products: [
      .executable(
            name: "todo-bot",
            targets: ["todo-bot"]
      ),
    ],
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
      .package(url: "https://github.com/zmeyc/telegram-bot-swift.git", from: "2.0.0"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages this package depends on.
        .executableTarget(
            name: "todo-bot",
            dependencies: [
              .product(name: "TelegramBotSDK", package: "telegram-bot-swift"),
            ]),
        .testTarget(
            name: "todo-botTests",
            dependencies: ["todo-bot"]),
    ]
)
{{< /highlight >}}

注意，在设置target 依赖时，要设置对象，指明依赖库的 `name` ： `.product(name: "TelegramBotSDK", package: "telegram-bot-swift"),`  

在调制 `main.swift` 文件时，部分接口已经过时，更新为最新：  

{{< highlight swift "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
//从环境变量中读取token
//let token = readToken(from: "iTodos_BOT_TOKEN")
//token 格式： 数字:字母
let token = "*****:AAHqvrTKBtQcNHHreYWHmEW****"
let bot = TelegramBot(token: token)

while let update = bot.nextUpdateSync() {
    if let message = update.message, let text = message.text, let from = message.from {
        bot.sendMessageAsync(chatId: .chat(from.id), text: "Hi \(from.firstName)! You said: \(text).\n")
    }
}

fatalError("Server stopped due to error: \(bot.lastError)")
{{< /highlight >}}

执行build 并运行：  

{{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
swift build
./.build/x86_64-apple-macosx10.10/debug/iTodos-bot

#打印：
>endpoint: getMe, data:
>Unable to fetch bot information: Libcurl error 28: Timeout was reached
{{< /highlight >}}
