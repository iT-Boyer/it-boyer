---
title: fork印象笔记SDK支持pod安装
date: 2018-10-21 00:12:20
updated: 2018-10-21 09:03:35
comments: true
tags: [pod]
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
{% github it-boyer evernote-sdk-mac fd5da70 width = 30% %}
## 制作pod支持
1. fork 并clone代码
```
git clone https://github.com/evernote/evernote-sdk-mac.git
```
2.  创建pod spec索引文件
```
$ cd evernote-sdk-mac
$ pod spec create EvernoteSDK https://github.com/it-boyer/evernote-sdk-mac.git
```
3. 编写配置文件
设置支持的平台，源码目录位置，指定忽略的文件等配置。
```
....
spec.osx.deployment_target = "10.7"
...
# ――― Source Code ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
spec.source_files  = "EvernoteSDK", "EvernoteSDK/**/*.{h,m}"
spec.exclude_files = "EvernoteSDK/internal/ENOAuthViewController*"

# ――― Project Settings ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
#spec.requires_arc = true
#spec.xcconfig = {"WARNING_CFLAGS" => '-Wno-nullability-completeness'}
# spec.xcconfig = { "HEADER_SEARCH_PATHS" => "$(SDKROOT)/usr/include/libxml2" }
end
```
4. 验证EvernoteSDK.podspec
需要用参数：--allow-warnings ，由于源码验证过程中的警告提示问题，导致验证失败
```
$ pod lib lint --allow-warnings
```
5. 发布到私库中
先在本地添加私库
```
$ pod repo add PodRepo https://github.com/it-boyer/PodRepo.git
#输出：
> Cloning spec repo `PodRepo` from `https://github.com/it-boyer/PodRepo.git`
```
开始发布过程中，也会验证，出现警告问题，需要添加`--allow-warnings`
```
$ pod repo push podRepo EvernoteSDK.podspec --allow-warnings
```
6. 在项目中使用
编辑podfile文件
```
#加载私库
source 'https://github.com/it-boyer/PodRepo.git'
#依赖库
pod 'EvernoteSDK', '~> 1.2.0'
```
在执行pod install 即可。
## API调用
### 在objc中调用
直接引入`#import <EvernoteSDK/EvernoteSDK.h>`
```objc
#import <EvernoteSDK/EvernoteSDK.h>
@implementation hello
-(void)test
{
    NSString *EVERNOTE_HOST = BootstrapServerBaseURLStringSandbox;
    NSString *CONSUMER_KEY = @"your key";
    NSString *CONSUMER_SECRET = @"your secret";
    [EvernoteSession setSharedSessionHost:EVERNOTE_HOST consumerKey:CONSUMER_KEY consumerSecret:CONSUMER_SECRET];
}
@end
```
### 在swift中调用
1. 创建`工程名-Bridging-Header.h`
引入框架库
```objc
#import <EvernoteSDK/EvernoteSDK.h>
```
2. 在main.swift 调用
```swift
import Foundation
let EVERNOTE_HOST = BootstrapServerBaseURLStringSandbox
let CONSUMER_KEY = ""
let CONSUMER_SECRET = ""
EvernoteSession.setSharedSessionHost(EVERNOTE_HOST, consumerKey: CONSUMER_KEY, consumerSecret: CONSUMER_SECRET)
```
