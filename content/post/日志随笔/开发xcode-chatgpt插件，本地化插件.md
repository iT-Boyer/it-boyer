+++
title = "在xcode 项目中添加本地化，支持中文"
description = "博客简介"
date = 2023-04-16T19:12:00+08:00
lastmod = 2023-04-16T19:18:47+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

在使用ChatGPT-xcode 时，缺少中文支持，导致在使用chatgpt 解释 swift 代码返回的是英文。 


## 如何插件也支持中文，以中文为默认语言和chatgpt 交流。 {#如何插件也支持中文-以中文为默认语言和chatgpt-交流}

<div class="ai">

[SYS]: 你熟练xcode swift 的开发者 

[ME]: 如何插件也支持中文，以中文为默认语言。 

[AI]: 要让插件支持中文，需要将插件本身的语言设置为中文，并将中文设置为默认语言。以下是设置步骤： 

1.  打开 Xcode，选择你的插件项目。

2.  单击左侧的项目文件列表，选择你的插件目标。

3.  在“项目”菜单中，选择“Info”。

4.  在 Info.plist 文件中，添加一条键为 “Localization native development region” 的项。将值设置为 “zh-Hans”。

5.  在“Supporting Files”文件夹下，添加一个名为 “zh-Hans.lproj”的文件夹，用于储存中文本地化资源文件。

6.  在该文件夹下，创建一个名为 “Localizable.strings”的文件。这个文件将包含所有需要本地化的字符串。

7.  右键点击该文件，选择 “Open As” -&gt; “ASCII Property List”。这将把文件的格式改变为一个结构化的属性列表。

8.  在属性列表中添加需要本地化的字符串和它们对应的中文翻译。

9.  在代码中使用 NSLocalizedString 函数调用本地化字符串，例如：

```swift NSLocalizedString("myString", comment: "") ``` 

1.  编译并运行你的插件，它现在应该以中文为默认语言了。

如果你需要添加其他语言支持，可以按照上述步骤为每种语言添加本地化资源。 

[ME]:

</div>


## 下面执行第8 步，翻译工作，使用chatgpt 翻译成中文。 {#下面执行第8-步-翻译工作-使用chatgpt-翻译成中文}

<div class="ai">

[SYS]: 你是swift 开发人员. 

[ME]: 翻译成中文 Convert this JSON to a Swift Codable data structure only struct declaration 

[AI]: 将这个 JSON 转换为 Swift Codable 数据结构，只能使用 struct 声明。 

[ME]: Check for Code Smells in this Swift code 

[AI]: 把这段 Swift 代码检查一下 Code Smells。 

[ME]: Write the test cases in Swift for this code using the XCTest framework. 

[AI]: 使用 XCTest 框架编写这段代码的 Swift 测试用例。 

[ME]: Generate a regular expression using Regex to match 

[AI]: 使用正则表达式（Regex）生成一个匹配的规则。 

可是您没有给出需要匹配的内容，如果您提供需要匹配的内容，我可以帮您生成匹配规则。 

[ME]:Generate comments in Swift format for this function, indicating the parameters and return type, with a max line lenght of 80 characters 

[AI]: 为这个函数生成 Swift 格式的注释，包含参数和返回类型说明，每行最大长度不超过 80 个字符。 

``` /\*\* Calculates the sum of two integers. 

-   Parameters: 
    -   a: The first integer to be added.
    -   b: The second integer to be added.

-   Returns: The sum result as an integer. \*/

func sum(\_ a: Int, _ b: Int) -&gt; Int { return a + b } 
``` 

注释分为两部分：函数概述和参数及返回值说明。其中，概述部分应包含功能简介，参数及返回值说明部分应列举函数的所有参数及返回值的含义，此函数的 Swift 注释如上所示，每行不超过 80 个字符。 

[ME]:

</div>

