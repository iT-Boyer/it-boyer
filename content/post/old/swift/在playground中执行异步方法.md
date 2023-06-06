---
title: 在playground中执行异步方法
date: 2018-10-12 19:56:59
updated: 2018-10-12 19:56:59
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
[PLAYGROUND 延时运行](http://swifter.tips/playground-delay/)引入 Playground 的`XCPlayground`扩展包框架,其中就包括使 Playground 能延时执行的黑魔法，`needsIndefiniteExecution`(需要无限期执行)使 Playground 具有延时运行的功能.
在实际使用和开发中，我们最经常面临的异步需求可能就是网络请求了，如果我们想要在 Playground 里验证某个 API 是否正确工作的话，使用 XCPlayground 的这个方法开启延时执行也是必要的：
```swift
let url = NSURL(string: "http://httpbin.org/get")!
let getTask = URLSession.shared.dataTask(with: url as URL) {
    (data, response, error) -> Void in
    let dictionary = try! JSONSerialization.jsonObject(with: data!, options: [])
    print(dictionary)
}
getTask.resume()
```
之前有30s限制，目前可以无限执行。
~~可以通过 Alt + Cmd + 回车 来打开辅助编辑器。在这里你会看到控制台输出和时间轴，将右下角的 30 改成你想要的数字，就可以对延时运行的最长时间进行设定了。 ~~
