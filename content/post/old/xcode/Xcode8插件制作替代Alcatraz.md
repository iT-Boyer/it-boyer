---
title: Xcode8插件制作替代Alcatraz
date: 2017-05-15 14:09:30
updated: 2019-01-16 21:01:14
comments: true
tags: [工具]
categories:
- 解决方案
keywords: 
permalink: 
---
## 背景
1. Xcode7插件工具Alcatraz
开发者可以在Xcode运行的时候通过注入代码去实现插件的功能。插件可以在一个Alcatraz这个优秀的APP上面提交和分发。
以往Xcode插件开发在没有官方支持的情况下，提供了[Xcode-Plugin-Template](https://github.com/kattrali/Xcode-Plugin-Template)和各种dump好的头文件，我们仍然需要在没有文档的情况下做各种猜测和hook。关于插件的开发，可以看看[这篇文章](https://onevcat.com/2013/02/xcode-plugin/)。
2. Xcode8编辑源码的插件Xcode source editor extensions
Xcode 8验证每个库和包，以防止恶意代码未经您的许可运行。苹果公司在今年的WWDC上宣布了可以通过开发`Xcode source editor extensions`来扩展以下三个功能:
* Add commands to the source editor // 给Xcode的代码编辑器扩展一些命令（在Editor菜单下增加额外的菜单）
* Edit text                 //通过这些命令对源代码进行编辑
* Change selections //对选中文本的编辑功能

## 源码编辑器插件
## 添加命令菜单
### 通过Info.plist中添加菜单
打开`Info.plist`，展开`NSExtension`至`XCSourceEditorCommandDefinitions`，命令菜单的定义如下：
```json
<key>NSExtensionAttributes</key>
<dict>
    <key>XCSourceEditorCommandDefinitions</key>
    <array>
        <dict>
            <key>XCSourceEditorCommandClassName</key>
            <string>$(PRODUCT_MODULE_NAME).SourceEditorCommand</string>
            <key>XCSourceEditorCommandIdentifier</key>
            <string>com.xt.APPlugins.AutoComment.SourceEditorCommand</string>
            <key>XCSourceEditorCommandName</key>
            <string>Source Editor Command</string>
        </dict>
    </array>
    <key>XCSourceEditorExtensionPrincipalClass</key>
    <string>$(PRODUCT_MODULE_NAME).SourceEditorExtension</string>
</dict>
```
其中：
* XCSourceEditorCommandClassName指向命令的处理类，该类实现XCSourceEditorCommand。
* XCSourceEditorCommandIdentifier是命令的唯一标识，通常我们会对一组命令设置同一个XCSourceEditorCommand，在invocation中获取此标识做区分处理。
* XCSourceEditorCommandName是命令在菜单栏展示的菜单名称。
### 通过代码定制命令菜单
此外，菜单项是动态加载的，可以实现`SourceEditorExtension.swift`的`commandDefinitions:`实现
`SourceEditorExtensio`n用来管理extension的生命周期相关,`SourceEditorCommand`则用来处理具体的命令。
这会覆盖掉Info.plist中的定义。
```swift
//1. 启动extension被调用，自定义相关操作
func extensionDidFinishLaunching(){}

var commandDefinitions: [[XCSourceEditorCommandDefinitionKey: AnyObject]] {
    // If your extension needs to return a collection of command definitions that differs from those in its Info.plist, implement this optional property getter.
    ////commandDefinitions属性的getter方法可以动态的展示或是隐藏特定的指令
    return [[.classNameKey: "SourceEditorCommand",
             .identifierKey: "CustomIdentifier",
             .nameKey: "CustomeName"]]
}
```
## 命令插件功能实现
打开`SourceEditorCommand.swift`，实现`perform(with:completionHandler:)`。
```swift
class SourceEditorCommand: NSObject, XCSourceEditorCommand
{
    //当通过Xcode菜单键调用插件时调用，实现插件功能的主体
    func perform(with invocation: XCSourceEditorCommandInvocation,
               completionHandler: @escaping (Error?) -> Void ) -> Void {
       // 正则匹配含有 闭包 的文本
       var updatedLineIndexes = [Int]()
       for lineIndex in 0 ..< invocation.buffer.lines.count
       {
           let line = invocation.buffer.lines[lineIndex] as! NSString
           do {
               let results = try findClosureSyntax(line: line)
               //简化所有闭包语法格式：移除闭包里面括号
               _ = results.map { result in
                   let cleanLine = line.remove(characters: ["(", ")"], in: result.range)
                   updatedLineIndexes.append(lineIndex)
                   invocation.buffer.lines[lineIndex] = cleanLine
               }
           } catch {
               completionHandler(error as NSError)
           }
       }
       completionHandler(nil)
   }
   
   //使用正则表达式去遍历每一行代码是否含有闭包
   func findClosureSyntax(line:NSString) throws ->[NSTextCheckingResult]
   {
       let regex =  try NSRegularExpression(pattern: "\\{.*\\(.+\\).+in", options: .caseInsensitive)
       let range = NSRange(0 ..< line.length)
       return regex.matches(in: line as String, options: .reportProgress, range: range)
   }
}
```
`XCSourceEditorCommandInvocation`的`buffer`提供了当前选中的文件源代码中的信息，包括文件的`lines`, `selections`, `indentationWidth`等等，具体作用可以查看WWDC视频或者苹果的文档查看，在此就不一一介绍了。

## 测试
1. 选择scheme后编译运行自定义的extensions插件
2. 选择测试安装插件的Xcode
3. 点击run按钮会单独启动一个全新的Xcode
在新的Xcode实例中，创建一个新的工程或是打开一个存在的工程。
4. 然后执行`Editor > Clean Closure > Source Editor Command`，需要确保在当前的文件里面含有一个闭包。这样就可以看到如下的效果，刚才开发的extension工作了！


## 命令快捷键
设置快捷键去自动调用Clean Syntax命令
1. 打开Xcode的Preferences，选择Key Bindings ;
2. 搜索Clean Syntax，点击右边然后输入快捷键，例如：Command-Alt-Shift-+。


[WWDC2016之初识Xcode Source Editor Extension](http://wuwen1030.github.io/2016/06/23/WWDC2016之初识Xcode-Source-Editor-Extension/)
[xcode常用的插件清单](https://github.com/theswiftdev/awesome-xcode-extensions)
[使用 Xcode Source Editor Extension开发Xcode 8 插件](http://www.iseedog.com/2016/09/21/xcode-source-editor/#more)
[详解一步步实现Xcode 8 插件](https://www.jianshu.com/p/9c9d0fcc62cc?winzoom=1)

