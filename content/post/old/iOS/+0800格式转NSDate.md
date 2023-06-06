---
title: +0800格式转NSDate
date: 2018-05-30 18:42:20
updated: 2018-05-30 18:42:20
comments: true
tags: [iOS]
categories:
- 解决方案
keywords: 
permalink: 
---
```objc
NSString *timstr = [resData objectForKey:@"Data"];
timstr = [timstr stringByReplacingOccurrencesOfString:@"/Date(" withString:@""];
timstr = [timstr stringByReplacingOccurrencesOfString:@"+0800)/" withString:@""];
model.time = [NSDate dateWithTimeIntervalSince1970:timstr.longLongValue/1000];
```
