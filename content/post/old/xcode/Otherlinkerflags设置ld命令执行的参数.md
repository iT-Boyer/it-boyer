---
title: Otherlinkerflags设置ld命令执行的参数
date: 2018-04-11 16:42:57
updated: 2018-04-11 19:16:17
comments: true
tags: [xcode]
categories:
- 解决方案
keywords: 
permalink: 
---
Other linker flags设置的值实际上就是ld命令执行时后面所加的参数。


3个常用参数：
`-ObjC`：加了这个参数后，链接器就会把静态库中所有的Objective-C类和分类都加载到最后的可执行文件中
`-all_load`：会让链接器把所有找到的目标文件都加载到可执行文件中，但是千万不要随便使用这个参数！假如你使用了不止一个静态库文件，然后又使用了这个参数，那么你很有可能会遇到`ld: duplicate symbol`错误，因为不同的库文件里面可能会有相同的目标文件，所以建议在遇到`-ObjC`失效的情况下使用`-force_load`参数。
`-force_load`：所做的事情跟`-all_load`其实是一样的，但是`-force_load`需要指定要进行全部加载的库文件的路径，这样的话，你就只是完全加载了一个库文件，不影响其余库文件的按需加载.

### 加载FrameWork


### 加载静态库
