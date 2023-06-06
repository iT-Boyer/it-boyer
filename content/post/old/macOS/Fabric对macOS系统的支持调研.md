---
title: Fabric对macOS系统的支持
date: 2017-02-14 11:14:35
updated: 2017-02-14 14:33:17
comments: true
tags: [SDK]
categories:
- 解决方案
keywords: Fabric,集成,发布
permalink: 
---
## Fabric
`Fabric` 是Twitter的移动应用开发平台，一个模块化、跨平台的移动开发套件，该博文主要研究`crashlytics`在app中的运用。
[注册新的账户](https://try.crashlytics.com/)登录，审核通过时间为几个小时或者1到2天不等。然后注册时候输入的邮箱就会收到如下的邀请涵
[浏览官方文档](https://docs.fabric.io/apple/crashlytics/os-x.html#macos-support)
### crashlytics支持macOS
对`macOS`的支持中出现的问题
问题：`NSApplicationCrashOnExceptions` is not set. This will result in poor `top-level` uncaught exception reporting
官方解释：
{% blockquote 官方文档 https://docs.fabric.io/apple/crashlytics/os-x.html#macos-support macOS Support %}
Uncaught Exceptions
Intercepting and reporting uncaught exceptions on macOS is more complex than it is on iOS. On macOS, AppKit will catch exceptions thrown on the main thread, preventing the application from crashing, but also preventing Crashlytics from reporting them. To make matters worse, Apple’s frameworks are not exception safe. This means that while AppKit will prevent your app from crashing, it will also likely corrupt your process’s state, often including AppKit itself. Typically, once an exception involving UI interaction is thrown, it will prevent your app from working correctly from that moment on.
Thankfully, AppKit has a little-known feature you can turn on to make the behavior much more predictable. We strongly recommend that you do the following in your application, right before you initialize Crashlytics.
{% codeblock lang:objc %}
[[NSUserDefaults standardUserDefaults] registerDefaults:@{ @"NSApplicationCrashOnExceptions": @YES }];
{% endcodeblock %}

This will make your application’s behavior much closer to iOS. It will mean that your app will crash on uncaught exceptions, and will also allow Crashlytics to report them with useful stack traces. It will also give you the ability to override this behavior with the user defaults system, even on per-user basis.
Of course, this is all optional. Crashlytics will warn about NSApplicationCrashOnExceptions not being set, but will otherwise preserve normal AppKit behavior by default.
{% endblockquote %}
拦截和报告未捕获的异常在MacOS比iOS更复杂。在MacOS，AppKit会抓住扔在主线程异常，防止应用程序崩溃，也防止crashlytics报告他们。更糟糕的是，Apple’s frameworks也不例外。这意味着AppKit可以防止你的应用程序崩溃，也可能破坏你的进程的状态，包括AppKit本身。通常情况下，一旦涉及到用户界面交互的异常被抛出，它将阻止您的应用程序从正确的工作从那一刻起。
以上情况，可以通过设置：`NSApplicationCrashOnExceptions` 改变系统默认的值，达到像iOS端一样抓去异常。如果没有设置会提示：NSApplicationCrashOnExceptions not set
### [Cause a Test Crash教程](https://docs.fabric.io/apple/crashlytics/test-crash.html)
崩溃日志是在重启APP程序的同时进行的，要确保程序在前台全屏显示。
Xcode调试器会阻止我们捕捉崩溃报告，所以在抓取崩溃报告时，要保证在非调试状态下进行。如果你的移动设备连接到Mac机上，Xcode仍然可以进入调试状态。
1. `run`在模拟器上安装运行APP
2. 点击`Stop`断开Xcode和模拟器，确保在非调试状态
3. 在模拟器上启动APP，并执行崩溃操作
4. 在Xcode上点击`run`
在Xcode的控制台console中输出显示奔溃报告，以及上传日志的相关提示。
macOS系统中的日志目录：
运行日志目录：/private/var/log/system.log
奔溃日志目录：/Users/pyc/Library/Logs/DiagnosticReports/APPNAME_2016-11-10-165115.crash

{% blockquote  官方文档 https://docs.fabric.io/apple/crashlytics/test-crash.html Cause a Test Crash教程 %}
It’s possible, but rare, that we are missing a dSYM to symbolicate any crash reports. There will be an alert on your dashboard if this is the case. Click through to upload the missing dSYM. Keep in mind that exceptions are not guaranteed to crash. The full code path, including code in system libraries, matters here. If you aren’t seeing the dSYM alert, you can go to your app’s settings page, and append “/mappings” to the URL to reach it, e.g. https://fabric.io/settings/apps/some_app_id/mappings
{% endblockquote %}
