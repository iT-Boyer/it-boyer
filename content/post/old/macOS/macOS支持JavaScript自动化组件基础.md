---
title: macOS支持JavaScript自动化组件基础
date: 2017-02-15 17:19:30
updated: 2017-02-16 13:43:01
comments: true
tags: [JS]
categories:
- 解决方案
keywords: JavaScript,工具,语法,管理,混编
permalink: 
---

苹果 `OS X Yosemite系统`把 `JavaScript`作为` AppleScript`的另一选择。`Automation` 是 `OS X 10.10版本`中的新特性，苹果在官网发布[JavaScript for Automation Release Notes](https://developer.apple.com/library/prerelease/mac/releasenotes/InterapplicationCommunication/RN-JavaScriptForAutomation/index.html#//apple_ref/doc/uid/TP40014508)有篇文章介绍了用`JavaScript`写自动化`Automation`脚本。
`JavaScript`凭借其简单的语法，良好的性能，超轻量的框架，极小耦合的模块系统等等优势已经吸引了很多大厂的关注。
再加上`JavaScript`先天开放且无版权专利纠纷的问题，拥有非常广泛的开发者（开源）群体，苹果将其引入`OS X平台`代替私有的`AppleScript`，可能也有着一部分开放性平台的考虑，从而吸引广泛的第三方开发者。
资源
[JavaScript OS X App Examples](https://github.com/tylergaw/js-osx-app-examples)
[JavaScript for Automation Cookbook](https://github.com/dtinth/JXA-Cookbook)

## OSA框架:`Open Scripting Architecture`
`OSA`组件用于实现MacAPP自动化操作。这些框架组件使用场景包括：`Script Editor`编辑器，全系统的`Script菜单`，` Run JavaScript Automator`命令的操作，`applets`小程序，命令行`osascript`工具，`NSUserScriptTask API`中，还可以运用在其他的`OSA`组件中例如：`AppleScript`。这也就包括了`Mail`规则、`Folder`操作、`Address Book`插件、日历闹钟和消息触发器。
## 脚本字典 
脚本字典详细介绍APP的对象模型。在脚本字典映射到有效的JavaScript标识符遵循一套规范的术语。在`Script Editor`脚本字典浏览器已经更新到显示术语`AppleScript`，`JavaScript`和O`bjective-C`（Scripting Bridge framework）格式。
### 打开脚本字典
启动`Script Editor` (/Applications/Utilities/) -->` File > Open Dictionary or Window > Library`。

## object specifier
在`JavaScript自动化主机`环境中的大部分对象指的是外部实例，如：其他APP，window或在这些APP的相关数据。当访问一个APP对象或APP中的某个元素的`JavaScript属性`时，会返回一个新的`object specifier`，也就是这个对象的`specifier 属性`。
> object specifier不是外部实例属性的实际值，是这个对象的引用指针。如果要获取这个属性的实际值，使用get／set方法。

## 访问APP
六种方式:
{% codeblock By name lang:js %}
Application('Mail')
{% endcodeblock %}

{% codeblock By bundle ID lang:js %}
Application('com.apple.mail')
{% endcodeblock %}

{% codeblock By path lang:js %}
Application('/Applications/Mail.app')
{% endcodeblock %}

{% codeblock By process ID lang:js %}
Application(763)
{% endcodeblock %}

{% codeblock On a remote machine lang:js %}
Application('eppc://127.0.0.1/Mail')
{% endcodeblock %}

{% codeblock currentApplication lang:js %}
Application.currentApplication()
{% endcodeblock %}

## 语法示例
{% codeblock Access properties lang:js %}
Mail.name
{% endcodeblock %}

{% codeblock Access elements lang:js %}
Mail.outgoingMessages[0]
{% endcodeblock %}

{% codeblock Call commands lang:js %}
Mail.open(...)
{% endcodeblock %}

{% codeblock Create new objects lang:js %}
Mail.OutgoingMessage(...)
{% endcodeblock %}

### 属性的get/set方法
点运算符访问脚本对象，是JavaScript语法特性之一。
如上所述，返回的对象是一个`object specifier`是一个对象的引用，而不是属性实际值。
当访问属性时，会作为一个get函数，返回实际值：
{% codeblock lang:js %}
subject = Mail.inbox.messages[0].subject()
{% endcodeblock %}
当赋值属性时，会作为一个set函数，把参数赋值该属性：
{% codeblock lang:js %}
Mail.outgoingMessages[0].subject = 'Hello world'
{% endcodeblock %}
获取数组中的每个元素属性（在这种情况下，得到邮件收件箱中的每份邮件的标题）
{% codeblock  lang:js %}
subjects = Mail.inbox.messages.subject()
{% endcodeblock %}

### 元素数组
通过在数组中调用特定元素检索方法，或使用方括号并指定要检索的元素的名称或索引来访问数组中的元素。返回值是对象相关，与自己的属性和元素，引用数组元素。他们可以访问
{% codeblock 索引 lang:js %}
window = Mail.windows.at(0)
window = Mail.windows[0]
{% endcodeblock %}
{% codeblock  name lang:js %}
window = Mail.windows.byName('New Message')
window = Mail.windows['New Message']
{% endcodeblock %}
{% codeblock ID lang:js %}
window = Mail.windows.byId(412)
{% endcodeblock %}
> Note: 使用ID来访问不是方括号[]而是().

### 调用命令
命令被称为函数。
2. 直接参数的函数，该参数作为命令的第一个参数传递。
3. 如果函数需要带参数名的参数，那么这个参数可以接受一个键值对对象。
3. 如果函数需要一个直接参数，就需要传递一个带参数名的参数作为第二个参数。
4. 如果函数不存在直接参数，那么带参数名的参数作为第一个参数传递，并且唯一参数。
5. 直接参数是可选的，可以不用传递任何值，当第一个参数存在参数名时，则传递NULL作为第一个参数。
{% codeblock 无参数命令 lang:js %}
message.open()
{% endcodeblock %}

{% codeblock 无参数名的命令 lang:js %}
Mail.open(message)
{% endcodeblock %}

{% codeblock 带参数名的命令 lang:js %}
response = message.reply({
replayAll: true,
openingWindow: false
})
{% endcodeblock %}

{% codeblock Command with direct parameter and named parameters  lang:js %}
Safari.doJavaScript('alert("Hello world")', {
in: Safari.windows[0].tabs[0]
})
{% endcodeblock %}

## Creating Objects
通过调用`类构造函数`初始化`属性`和`数据`来创建新对象。
在创建对象时,需要执行的其中步骤：
1. `make()`方法：调用对象上的`make()`方法来实例化对象。
2. `push()`方法：调用对象数组上的`push`方法来实例化对象。
在调用这些方法中的一个之前，对象实际上并不存在于应用程序中。

### Create a new object.
{% codeblock lang:js %}
message = Mail.OutgoingMessage().make()
{% endcodeblock %}

### Create a new object with properties.
{% codeblock  lang:js %}
message = Mail.OutgoingMessage({
subject: 'Hello world',
visible: true
})
Mail.outgoingMessages.push(message)
{% endcodeblock %}

### Create a new object with data.
{% codeblock lang:js %}
para = TextEdit.Paragraph({}, 'Some text')
TextEdit.documents[0].paragraphs.push(para)
{% endcodeblock %}

### 使用对象
一旦你在应用程序中创建一个新的对象（通过调用`make`或`push`），可以像任何现有的应用程序对象一样进行交互。
{% codeblock lang:js %}
message = Mail.OutgoingMessage().make()
message.subject = 'Hello world'
{% endcodeblock %}

### Scripting Additions
使用脚本添加（脚本插件）来增强应用程序的功能。操作系统有一套标准的脚本添加提供speak text,展示用户交互对话，等。
使用这些，必须明确设置`includeStandardAdditions`的`flag`为 `true`。
{% codeblock lang:js %}
app = Application.currentApplication()
app.includeStandardAdditions = true
app.say('Hello world')
app.displayDialog('Please enter your email address', {
withTitle: 'Email',
defaultAnswer: 'your_email@site.com'
})
{% endcodeblock %}

## Applets
在`Script Editor`编写脚本并保存为一个应用程序，且可以被双击独立运行的程序称为`Applet`。
程序支持以下事件处理：
当Applet运行时，`run`处理事件被调用：
{% codeblock lang:js %}
function run() {...}
{% endcodeblock %}

用于拖放操作的`openDocuments`处理事件程序包配置小程序，当文档被拖放到该小程序上时，这个处理操作将被执行：
{% codeblock  lang:js %}
function openDocuments(docs) {...}
{% endcodeblock %}
传递的参数是一个文件路径字符串数组。
[更多样例](https://developer.apple.com/library/content/releasenotes/InterapplicationCommunication/RN-JavaScriptForAutomation/Articles/OSX10-10.html#//apple_ref/doc/uid/TP40014508-CH109-SW8)

## UI Automation
通过编写系统事件应用程序，可以自动化应用程序的用户界面。在脚本编辑器`Script Editor`中浏览`System Events`的脚本字典，特别是进程套件`Processes Suite`，以查看支持此类型自动化的应用程序接口元素的列表。
下面的示例使用UI脚本创建Notes中的新注释。
{% codeblock lang:js %}
Notes = Application('Notes')
Notes.activate()

delay(1)
SystemEvents = Application('System Events')
Notes = SystemEvents.processes['Notes']

Notes.windows[0].splitterGroups[0].groups[1].groups[0].buttons[0].click()
{% endcodeblock %}
