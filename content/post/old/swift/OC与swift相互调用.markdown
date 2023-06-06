---
layout: post
title: "OC与swift相互调用"
date: 2015-12-01 11:42:18 +0800
comments: true
tags: [swift,oc]
keywords: swift,objc,桥连接
categories:
- 解决方案
---
#### Swift中使用OC的类声明  -- 实现配置 桥接的头文件
###### 方式一：自动添加桥接头文件
1. 在一个全新的Swift，利用第一次新建提示的方式自动添加桥接头文件。
2. 点确定这后就会生成一个以<produceName-Bridging-Header.h>的头文件。
3. 在targets->build settings ->Object-C Bridging Header 设为生成的个桥接的头文件即可。
4. 把想要在swift类中调用的OC头文件放使用import "" 写到这个桥接文件中：
```objc
	//  Use this file to import your target's public headers that you would like to expose to Swift.  
	//MixDemo/MixDemo-Bridging-Header.h    
	#import "OCChannel.h"  
```

###### 方式二：手动添加桥接头文件
同样的，当你知道这个swift搜索头文件的关系后，就不需要再理会这个-Bridging-Header.h的文件了。
完全可以手工建一个并取自己喜欢的名字：
1. 新建一个头文件，名为:OCContainerHeader.h
2. 在targets->build settings ->Object-C Bridging Header 设为生成的个桥接的头文件即可。
3. 把想要在swift类中调用的OC头文件放使用import "" 写到这个桥接文件中：
```objc
//  Use this file to import your target's public headers that you would like to expose to Swift.  	
//MixDemo/MixDemo-Bridging-Header.h    
#import "OCChannel.h"  
```

#### OC如何调用Swift写的类  -- 	为了在 Objective-C 中可用， Swift 类必须是 Objective-C 类的子类，或者用 @Objective-C 标记；
1. 选中targets->build settings ->packing->Product Module Name 中设置模块名（可以自定义），这个名称很重要 swift 的头文件就是根据这个来命名的，例如：SwiftModule。
2. 在OC头文件类中，添加import "SwiftModule-swift.h"但你在整个工程中是找不到这个文件的，但可以使用CMD+ 鼠标点击可看这个头文件中的内容。  

#### 总结：
这样，工程中如查Swift要使用OC,则把需要使用的OC类的头文件，全写在MixDemo-Bridging-Header.h里。同样如果OC中所使用的swift类，只需要Clean一把，再编就可以了，但不要忘了导入SwiftModule-swift.h哦（名称自取，但-swift.h是固定的），另外还有一个需要读者注意的。

注：
	凡是用Swift写的类，如果不继成自NSObject或NSObject 的派生类，哪么编译后将不会生成对应的转换类。从而使得OC 中找不到相应的声明。
如我的例子中 class Act 这样不会被编译到SwiftModule-swift.h中，但写为 class Act : NSObject，就可以编译出相应的声明。另外可以使用@objc加以声明，但这个还是一样，类最好继承NSObject下来。
