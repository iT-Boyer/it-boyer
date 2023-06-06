---
title: 通过沙盒中JS脚本访问其他APP
date: 2017-02-14 18:25:29
updated: 2018-10-15 17:22:37
comments: true
tags: [JS]
categories:
- 解决方案
keywords: []
permalink:  
---
如何使用脚本字典里的命令和对象来与其他的应用进行通讯?
这个教程将向您展示现在使用 `AppleScript` 来控制别的应用的最佳方式。我也会告诉您一些小技巧以帮助您和您的用户用最小的努力就架设起 `AppleScript`。
## 在自己的APP中编写
### 编写AppleScript代码
[AppleScript 脚本指南](https://developer.apple.com/library/mac/documentation/applescript/conceptual/applescriptlangguide/introduction/ASLR_intro.html#//apple_ref/doc/uid/TP40000983-CH208-SW1)
与其他应用进行通讯的脚本一般来说都很短，也容易理解。`AppleScript` 可以被想做一种传送的机制，而不是一种处理环境。
典型脚本:
{% codeblock lang:js %}
on chockify(inputString)
    set resultString to ""

        repeat with inputStringCharacter in inputString
            set asciiValue to (ASCII number inputStringCharacter)
            if (asciiValue > 96 and asciiValue < 123) then
                set resultString to resultString & (ASCII character (asciiValue - 32))
            else
                if ((asciiValue > 64 and asciiValue < 91) or (asciiValue = 32)) then
                    set resultString to resultString & inputStringCharacter
                else
                    if (asciiValue > 47 and asciiValue < 58) then
                        set numberStrings to {"ZERO", "ONE", "TWO", "THREE", "FOR", "FIVE", "SIX", "SEVEN", "EIGHT", "NINE"}
                        set itemIndex to asciiValue - 47
                        set numberString to item itemIndex of numberStrings
                        set resultString to resultString & numberString & " "
                    else
                        if (asciiValue = 33) then
                            set resultString to resultString & " DUH"
                        else
                            if (asciiValue = 63) then
                                set resultString to resultString & " IF YOU KNOW WHAT I MEAN"
                            end if
                        end if
                    end if
                end if
            end if
        end repeat
        resultString
end chockify
{% endcodeblock %}

### 创建事件描述符 (event descriptor) 
1.  导入Carbon.h
它有关于所有的 AppleEvent 的定义。
{% codeblock lang:objc %}
#import <Carbon/Carbon.h> // for AppleScript definitions
{% endcodeblock %}
2. OC中创建`chockify`事件描述符
这是可以在你的脚本和应用之间互相传递的一个数据块。可以把它理解成一个封装好的会去执行某个事件的目标，一个将被调用的函数，以及这个函数的参数。使用一个 `NSString` 作为参数，创建`chockify`事件描述符：
{% codeblock lang:objc %}
- (NSAppleEventDescriptor *)chockifyEventDescriptorWithString:(NSString *)inputString
{
    // parameter
    NSAppleEventDescriptor *parameter = [NSAppleEventDescriptor descriptorWithString:inputString];
    NSAppleEventDescriptor *parameters = [NSAppleEventDescriptor listDescriptor];
    [parameters insertDescriptor:parameter atIndex:1]; // you have to love a language with indices that start at 1 instead of 0

    // target
    ProcessSerialNumber psn = {0, kCurrentProcess};
    NSAppleEventDescriptor *target = [NSAppleEventDescriptor descriptorWithDescriptorType:typeProcessSerialNumber bytes:&psn length:sizeof(ProcessSerialNumber)];

    // function
    NSAppleEventDescriptor *function = [NSAppleEventDescriptor descriptorWithString:@"chockify"];

    // event
    NSAppleEventDescriptor *event = [NSAppleEventDescriptor appleEventWithEventClass:kASAppleScriptSuite eventID:kASSubroutineEvent targetDescriptor:target returnID:kAutoGenerateReturnID transactionID:kAnyTransactionID];
    [event setParamDescriptor:function forKeyword:keyASSubroutineName];
    [event setParamDescriptor:parameters forKeyword:keyDirectObject];

    return event;
}
{% endcodeblock %}

### OC中加载 AppleScript
通过应用包(Application bundle)的一个 `URL` 可以创建 `NSAppleScript`的实例。而反过来，脚本也要和上面创建的 `chockify 事件描述符`一起使用。
{% codeblock  lang:objc %}
NSURL *URL = [[NSBundle mainBundle] URLForResource:@"Automation" withExtension:@"scpt"];
if (URL) 
{
    NSAppleScript *appleScript = [[NSAppleScript alloc] initWithContentsOfURL:URL error:NULL];

    NSAppleEventDescriptor *event = [self chockifyEventDescriptorWithString:[self.chockifyInputTextField stringValue]];
    NSDictionary *error = nil;
    NSAppleEventDescriptor *resultEventDescriptor = [appleScript executeAppleEvent:event error:&error];
    if (! resultEventDescriptor) 
    {
        NSLog(@"%s AppleScript run error = %@", __PRETTY_FUNCTION__, error);
    }
    else
    {
        NSString *string = [self stringForResultEventDescriptor:resultEventDescriptor];
        [self updateChockifyTextFieldWithString:string];
    }
}
{% endcodeblock %}
如果一切正常的话，你会得到另一个事件描述符。如果出错了，你会得到一个包含了描述错误信息的字典。虽说这个模式和很多其他 `Foundation 类`很相似，但是返回的错误并不是一个 `NSError` 的实例。

### 调用事件描述符
{% codeblock lang:objc %}
- (NSString *)stringForResultEventDescriptor:(NSAppleEventDescriptor *)resultEventDescriptor
{
    NSString *result = nil;
    if (resultEventDescriptor)
    {
        if ([resultEventDescriptor descriptorType] != kAENullEvent)
        {
            if ([resultEventDescriptor descriptorType] == kTXNUnicodeTextData) 
            {
                result = [resultEventDescriptor stringValue];
            }
        }
    }
    return result;
}
{% endcodeblock %}
InputString 输入可以被正确整形输出，并且你现在也看到想在你的应用里运行 AppleScripts 的方法

## 调用沙盒中脚本代码与访问其他应用
### 了解APP沙盒限制
如果一段脚本可以轻易地拿到浏览器当前页面上的内容，甚至是在任意标签和窗口运行`JavaScript`。想象一下如果这些页面里有你的银行账号，或者包含你的信用卡信息什么的。

对于沙盒应用，Apple 所提倡的是通过用户的需要来驱动安全策略。这意味着是否运行你的脚本完全取决于用户。这些脚本可能是来自互联网，也可能是你应用的一部分。一旦得到了权限，脚本就可以以一种受限的方式与系统其他部分进行交互了。`NSUserScriptTask`使这一切变得可能。
由此：Apple 引入了一个新的抽象类 `NSUserScriptTask`,有三个具体的子类实现:
1. `NSUserUnixTask`: 执行 Unix shell 命令
2. `NSUserAutomatorTask`: Automator 工作流
3. `NSUserAppleScriptTask`:执行`AppleScript脚本`,脚本是异步执行的,所以脚本不能对用户界面做更新操作。

### 开始安装运行脚本
怎么向用户请求运行脚本的许可，让你的应用与用户的其他应用更好地工作在一起？
两种策略:
1. 帮助用户来存放运行脚本的位置
2. 获取行脚本目录可读写

#### 帮用户存放运行脚本的位置
只能把把这些脚本放到用户的脚本文件夹(`User > Library > Application Scripts/bundle identifier/`)中，以只读的方式来运行你的脚本。
脚本想要进入这个特定的文件夹的唯一方式就是用用户把它们复制到那里。再者`Library 文件夹`在 OS X 里默认还是隐藏的。这样对用户都很不友好。 
让代码来帮助用户打开这个隐藏文件夹：
{% codeblock lang:objc %}
NSError *error;
NSURL *directoryURL = [[NSFileManager defaultManager] URLForDirectory:NSApplicationScriptsDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:YES error:&error];
[[NSWorkspace sharedWorkspace] openURL:directoryURL];
{% endcodeblock %}
通过你的应用的某个控件打开这个文件夹，然后进行编辑。这对于用户自己写的脚本来说是个很好的解决方案。

#### 设置运行脚本目录的读写权限
1. 在 Xcode 里，你需要更新 `Capabilities`，让其包括 `User Selected File to Read/Write`。在 `App Sandbox > File Access `里找到相关选项。
2. 用户的意愿是关键，因为你需要获取权限以将脚本添加到文件夹：
{% codeblock lang:objc %}
NSError *error;
NSURL *directoryURL = [[NSFileManager defaultManager] URLForDirectory:NSApplicationScriptsDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:YES error:&error];
NSOpenPanel *openPanel = [NSOpenPanel openPanel];
[openPanel setDirectoryURL:directoryURL];
[openPanel setCanChooseDirectories:YES];
[openPanel setCanChooseFiles:NO];
[openPanel setPrompt:@"Select Script Folder"];
[openPanel setMessage:@"Please select the User > Library > Application Scripts > com.iconfactory.Scriptinator folder"];

[openPanel beginWithCompletionHandler:^(NSInteger result) {
if (result == NSFileHandlingPanelOKButton) 
{
    NSURL *selectedURL = [openPanel URL];
    if ([selectedURL isEqual:directoryURL])
    {
        NSURL *destinationURL = [selectedURL URLByAppendingPathComponent:@"Automation.scpt"];
        NSFileManager *fileManager = [NSFileManager defaultManager];
        NSURL *sourceURL = [[NSBundle mainBundle] URLForResource:@"Automation" withExtension:@"scpt"];
        NSError *error;
        BOOL success = [fileManager copyItemAtURL:sourceURL toURL:destinationURL error:&error];
        if (success)
        {
            NSAlert *alert = [NSAlert alertWithMessageText:@"Script Installed" defaultButton:@"OK" alternateButton:nil otherButton:nil informativeTextWithFormat:@"The Automation script was installed succcessfully."];
            [alert runModal];
        }
        else 
        {
            NSLog(@"%s error = %@", __PRETTY_FUNCTION__, error);
            if ([error code] == NSFileWriteFileExistsError) 
            {
                // this is where you could update the script, by removing the old one and copying in a new one
            }
            else 
            {
                // the item couldn't be copied, try again
                [self performSelector:@selector(installAutomationScript:) withObject:self afterDelay:0.0];
            }
        }
    }
    else 
    {
        // try again because the user changed the folder path
        [self performSelector:@selector(installAutomationScript:) withObject:self afterDelay:0.0];
    }
}
}];
{% endcodeblock %}
这么一来，应用包中的 `Automation.scpt` 文件现在暴露在常规的文件系统中了。

### 执行脚本任务
使用 `NSUserAppleScriptTask` 来替代 `NSAppleScript`，来运行上面创建的`事件描述符`。
你大概会经常用到这些脚本任务。文档警告说对于给定的类的某个实例， `NSUserAppleScriptTask` 不应该被执行多次。所以写一个`工厂函数`来在需要的时候创建任务：
{% codeblock 工厂函数 lang:objc %}
- (NSUserAppleScriptTask *)automationScriptTask
{
    NSUserAppleScriptTask *result = nil;
    NSError *error;
    NSURL *directoryURL = [[NSFileManager defaultManager] URLForDirectory:NSApplicationScriptsDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:YES error:&error];
    if (directoryURL) 
    {
        NSURL *scriptURL = [directoryURL URLByAppendingPathComponent:@"Automation.scpt"];
        result = [[NSUserAppleScriptTask alloc] initWithURL:scriptURL error:&error];
        if (! result) 
        {
            NSLog(@"%s no AppleScript task error = %@", __PRETTY_FUNCTION__, error);
        }
    }
    else
    {
        // NOTE: if you're not running in a sandbox, the directory URL will always be nil
        NSLog(@"%s no Application Scripts folder error = %@", __PRETTY_FUNCTION__, error);
    }
    return result;
}
{% endcodeblock %}
> 如果你正在写一个同时适用于沙盒和非沙盒的 Mac 应用的话，在获取 `directoryURL` 时你需要特别小心。`NSApplicationScriptsDirectory`只在沙盒中有效。

在创建脚本任务后，你需要使用 `AppleEvent` 并提供一个结束处理来执行它：
{% codeblock AppleEvent lang:objc %}
NSUserAppleScriptTask *automationScriptTask = [self automationScriptTask];
if (automationScriptTask) 
{
    NSAppleEventDescriptor *event = [self safariURLEventDescriptor];
    [automationScriptTask executeWithAppleEvent:event completionHandler:^(NSAppleEventDescriptor *resultEventDescriptor, NSError *error) {
        if (! resultEventDescriptor) 
        {
            NSLog(@"%s AppleScript task error = %@", __PRETTY_FUNCTION__, error);
        }
        else
        {
            NSURL *URL = [self URLForResultEventDescriptor:resultEventDescriptor];
            // NOTE: The completion handler for the script is not run on the main thread. Before you update any UI, you'll need to get
            // on that thread by using libdispatch or performing a selector.
            [self performSelectorOnMainThread:@selector(updateURLTextFieldWithURL:) withObject:URL waitUntilDone:NO];
        }
    }];
}
{% endcodeblock %}
对于用户写的脚本，用户可能期望你的应用只是简单地'运行'脚本 (而不去调用事件描述符中指定的函数)。在这种情况下，你可以为 `event` 传递一个 `nil`，脚本就会像用户在 `Finder` 中双击那样的行为进行执行。
`NSUserAppleScriptTask`脚本是异步执行的，所以你的用户界面并不会被一个 (比较长) 的脚本锁住，在结束后会执行回调处理。
## 同步操作
`NSAppleScript` 和 `NSUserAppleScriptTask` 有一个微妙的区别：新的机制是异步执行的。对于大部分情况，使用一个结束回调来处理会是一个好得多的方式，因为这样就不会因为执行脚本而阻碍你的应用。
然而有时候如果你想带有依赖地来执行任务的时候，事情就变得有些取巧了。比方说一个任务需要在另一个任务开始之前必须完成。这种情况下你就会想念 `NSAppleScript` 的同步特性了。
要获得传统方式的行为，一种简单的方法是使用一个`信号量(semaphore)` 来确保同时只有一个任务运行、在你的类或者应用的初始化方法中，使用 `libdispatch` 创建一个信号量：
{% codeblock  lang:objc %}
self.appleScriptTaskSemaphore = dispatch_semaphore_create(1);
{% endcodeblock %}
接下来在初始化脚本任务之前，简单地等待信号量。当任务完成时，标记相同的这个信号量：
{% codeblock  lang:objc %}
// wait for any previous tasks to complete before starting a new one — remember that you're blocking the main thread here!
dispatch_semaphore_wait(self.appleScriptTaskSemaphore, DISPATCH_TIME_FOREVER);

// run the script task
NSAppleEventDescriptor *event = [self openNetworkPreferencesEventDescriptor];
[automationScriptTask executeWithAppleEvent:event completionHandler:^(NSAppleEventDescriptor *resultEventDescriptor, NSError *error) {
    if (! resultEventDescriptor)
    {
        NSLog(@"%s AppleScript task error = %@", __PRETTY_FUNCTION__, error);
    }
    else 
    {
        [self performSelectorOnMainThread:@selector(showNetworkAlert) withObject:nil waitUntilDone:NO];
    }
    // the task has completed, so let any pending tasks proceed
    dispatch_semaphore_signal(self.appleScriptTaskSemaphore);
}];
{% endcodeblock %}
再强调一下，除非确实有所需要，否则最好别这么做。
