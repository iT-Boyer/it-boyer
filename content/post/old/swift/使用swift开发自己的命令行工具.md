---
title: 使用swift开发自己的命令行工具
date: 2018-10-15 19:30:26
updated: 2018-10-15 19:54:51
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
<!--github库卡片-->
{% github it-boyer Panagram_Final 8023350 width = 30% %}
[Command Line Programs on macOS Tutorial](https://www.raywenderlich.com/511-command-line-programs-on-macos-tutorial)
[swift编写命令行工具](https://my.oschina.net/uniquejava/blog/685537?p=1)

    更新7/21/17:macOS教程上的命令行程序已经更新为Xcode 9和Swift 4。
## 前言
典型的Mac用户使用图形用户界面(GUI)与计算机交互。gui，顾名思义，是基于用户通过输入设备(如鼠标)与计算机进行视觉交互，通过选择或操作屏幕元素(如菜单、按钮等)。
不久前，在GUI出现之前，命令行接口(CLI)还是与计算机交互的主要方法。CLIs是基于文本的接口，用户可以在其中输入要执行的程序名，后面跟着参数。
尽管gui很流行，命令行程序仍然在当今的计算世界中扮演着重要的角色。命令行程序(如`ImageMagick`或`ffmpeg`)在服务器世界中非常重要。事实上，大多数形成Internet的服务器只运行命令行程序。
甚至Xcode也使用命令行程序!当Xcode构建项目时，它调用xcodebuild，它执行实际的构建。如果构建过程被嵌入到Xcode产品中，持续集成解决方案将很难实现，如果不是不可能的话!
## 开始
对于创建命令行程序来说，Swift似乎是一个奇怪的选择，因为C、Perl、Ruby或Java等语言是更传统的选择。但是，有一些很好的理由可以根据您的命令行需求选择Swift:
* Swift既可以用作解释脚本语言，也可以用作编译语言。这给了您脚本语言的优势，例如零编译时间和易于维护，以及选择编译应用程序以提高执行时间或将其捆绑销售。
* 你不需要学习其他语言。许多人说程序员应该每年学一门新语言。这不是一个坏主意，但是如果你已经习惯了Swift和它的标准库，你可以通过坚持使用Swift来减少时间投资。

## 样例介绍
在macOS教程中的命令行程序中，您将编写一个名为Panagram的命令行实用程序。根据传入的选项，它将检测给定输入是回文还是字谜。它可以从预定义的参数开始，或者在交互模式下运行，提示用户输入所需的值。
通常，命令行程序是从嵌入在工具应用程序(比如macOS中的Terminal)中的shell中启动的。为了简单易学，在本教程中，大部分时间您将使用Xcode来启动Panagram。在本教程的最后，您将学习如何从终端启动Panagram。
Open Xcode and go to` File/New/Project`. Find the `macOS` group, select `Application/Command Line Tool` and click Next:
![](https://koenig-media.raywenderlich.com/uploads/2017/06/New-Project-444x320.png)
For `Product Name`, enter `Panagram`. Make sure that Language is set to `Swift`, then click `Next`.
![](https://koenig-media.raywenderlich.com/uploads/2017/06/Name-the-New-Project.png)
In the Project Navigator area you will now see the `main.swift` file that was created by the `Xcode Command Line Tool` template.
![](https://koenig-media.raywenderlich.com/uploads/2017/06/main-swift.png)
Many C-like languages have a `main` function that serves as the entry point — i.e. the code that the operating system will call when the program is executed. This means the program execution starts with the first line of this function. Swift doesn’t have a `main` function; instead, it has a `main file`.
When you run your project, the first line inside the main file that isn’t a method or class declaration is the first one to be executed. It’s a good idea to keep your `main.swift` file as clean as possible and put all your classes and structs in their own files. This keeps things streamlined and helps you to understand the main execution path.
### The Output Stream
### Command-Line Arguments
### Anagrams and Palindromes
### Handle Input Interactively
### Xcode中测试
Launching Outside Xcode
Normally, a command-line program is launched from a shell utility like Terminal (vs. launching it from an IDE like Xcode). The following section walks you through launching your app in Terminal.
There are different ways to launch your program via Terminal. You could find the compiled binary using the Finder and start it directly via Terminal. Or, you could be lazy and tell Xcode to do this for you. First, you'll learn the lazy way.
1.  Launch your app in Terminal from Xcode
Create a new scheme that will open Terminal and launch Panagram in the Terminal window. Click on the scheme named Panagram in the toolbar and select New Scheme:
![](https://koenig-media.raywenderlich.com/uploads/2017/06/SelectNewScheme.png)

Name the new scheme `Panagram on Terminal`:
![](https://koenig-media.raywenderlich.com/uploads/2017/06/NameNewScheme.png)

Ensure the Panagram on Terminal scheme is selected as the active scheme. Click the scheme and select Edit Scheme... in the popover.
Ensure that the Info tab is selected and then click on the Executable drop down and select Other. Now, find the Terminal.app in your Applications/Utilities folder and click Choose. Now that Terminal is your executable, uncheck Debug executable.
Your Panagram on Terminal scheme's Info tab should look like this:
![](https://koenig-media.raywenderlich.com/uploads/2017/06/TerminalScheme-650x368.png)
>Note: The downside is that you can't debug your app in Xcode this way because now the executable that Xcode launches during a run is Terminal and not Panagram.

Next, select the Arguments tab, then add one new argument:
`${BUILT_PRODUCTS_DIR}/${FULL_PRODUCT_NAME}`
![](https://koenig-media.raywenderlich.com/uploads/2016/03/passed_arguments2-480x118.png)
Finally, click Close.
Now, make sure you have the scheme `Panagram on Terminal` selected, then build and run your project. Xcode will open Terminal and pass through the path to your program. Terminal will then launch your program as you'd expect.
![](https://koenig-media.raywenderlich.com/uploads/2016/03/Finished_Program-e1458913657510-700x267.png)

## 终端启动命令行APP
Open Terminal from your Applications/Utilities folder.
In the Project Navigator select your product under the Products group. Copy your debug folder's Full Path from Xcode's Utility area as shown below (do not include "Panagram"):
![](https://koenig-media.raywenderlich.com/uploads/2017/06/BuildFullPath.png)
Open a Finder window and select the Go/Go to Folder... menu item and paste the full path you copied in the previous step into the dialog's text field:
Click Go and Finder navigates to the folder containing the Panagram executable:
Drag the Panagram executable from Finder to the Terminal window and drop it there. Switch to the Terminal window and hit Return on the keyboard. Terminal launches Panagram in interactive mode since no arguments were specified:
![](https://koenig-media.raywenderlich.com/uploads/2016/03/Finished_Program-e1458913657510-700x267.png)
