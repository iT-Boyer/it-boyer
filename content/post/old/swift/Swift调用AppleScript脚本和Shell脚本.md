---
title: Swift调用AppleScript脚本和Shell脚本
date: 2018-10-15 17:22:37
updated: 2018-10-15 21:08:17
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
当想让自己的app支持播放，但有没有足够的时间来开发自己的播放器，可以先考虑用mpv播放器来实现相关功能，这是AppleScript作为互通的桥梁。
## NSTask调用AppleScript
### 用`Process`(原`NStask`)进行调用`main.scpt`脚本
2. swift两种方式
```swift
//方式一：
let process = Process()
process.launchPath = "/usr/bin/osascript"
process.arguments = ["~/Desktop/main.scpt"]
process.launch()

//方式二
let bundle = NSBundle.mainBundle()
if let scriptPath = bundle.pathForResource("main", ofType: "scpt"){
    let paths = [scriptPath]
    Process.launchedProcess(launchPath: "/usr/bin/osascript", arguments: paths)
}
```
### NSAppleScript调用AppleScript
`NSAppleScript`语法：`do shell script "shell语句"`，必须在`on run {变量名称，逗号隔开}` 以`end run`结束的闭包里运行。
调用APPleScript脚本片段样例：
```swift
let bundle = NSBundle.mainBundle()
let videoPath = bundle.pathForResource("BigBuck", ofType: "m4v")
//https://developer.apple.com/library/mac/technotes/tn2084/_index.html
//open -na /Applications/mpv.app命令行必须是 -na 才能调用当前指定的播放器，否则会调用系统默认播发器
let myAppleScript = "on run\ndo shell script \"open -na /Applications/mpv.app \(videoPath!)\"\ntell application \"mpv\" to activate\n end run"
print(myAppleScript)
var error: NSDictionary?
if let scriptObject = NSAppleScript(source: myAppleScript) {
    if let output: NSAppleEventDescriptor = scriptObject.executeAndReturnError(&error) {
        print(output.stringValue)
    } else if (error != nil) {
        print("error: \(error)")
    }
}
```
[oc调用AppleScript](http://stackoverflow.com/questions/25163433/can-you-execute-an-applescript-script-from-a-swift-application)
[Can you execute an Applescript script from a Swift Application](https://stackoverflow.com/questions/25163433/can-you-execute-an-applescript-script-from-a-swift-application)
                      
## AppleScript调用shell  
在`NSAppleScript`语法中调用shell语句
```
do shell script "shell语句"`
```
例：运行shell命令`open -na /Applications/mpv.app (videoPath!)`
```js
on run
    do shell script 
    "open -na /Applications/mpv.app (videoPath!)"
    tell application "mpv" to activate
end run
```


## 终端`osascript`调用`AppleScript`
[参考](http://www.hackmac.org/tutorials/run-applescript-from-the-command-line/)
### 语法
{% codeblock lang:shell %}
osascript -e 'applescript command' #单引号
{% endcodeblock %}
### 打开Finder窗口
{% codeblock lang:shell %}
osascript -e 'tell app "Finder" to make new Finder window'
{% endcodeblock %}
### 打开某个程序同时弹出"Hello World"提示框
{% codeblock  lang:js %}
osascript -e 'tell app "applicationname" to display dialog "Hello World"'
{% endcodeblock %}
### 设置音量，音量大小范围（0-7）
{% codeblock  lang:js %}
osascript -e "set volume number"
{% endcodeblock %}

## c语言调用shell
可以用c语言的`#include  system`
函数库：`include<stdlib.h>`
函数说明:
`system()`会调用`fork()`产生子进程，由子进程来调用`/bin/sh-c string`来执行参数`string`字符串所代表的命令，此命>令执行完后随即返回原调用的进程。在调用`system()`期间`SIGCHLD` 信号会被暂时搁置，`SIGINT`和`SIGQUIT` 信号则会被忽略。
返回值:
```
=-1:出现错误  
=0:调用成功但是没有出现子进程  
>0:成功退出的子进程的id
```
 如果`system()`在调用`/bin/sh`时失败则返回`127`，其他失败原因返回`-1`。若参数string为空指针(`NULL`)，则返回非零值>。如果`system()`调用成功则最后会返回执行shell命令后的返回值，但是此返回值也有可能为 `system()`调用`/bin/sh`失败所返回的`127`，因此最好能再检查`errno`来确认执行成功。
