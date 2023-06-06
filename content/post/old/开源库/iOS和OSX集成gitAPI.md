---
title: iOS和OSX集成gitAPI
date: 2017-05-17 17:33:46
updated: 2019-01-16 21:01:14
comments: true
tags: [SDK]
categories:
- 学习笔记
keywords: 
permalink: 
---

在项目中使用git submodule工具集成子项目ObjectiveGit

#### 在新项目中使用git submodule集成gitAPI   
参考官方提供的两个demo
* OS X: [CommitViewer](https://github.com/Abizern/CommitViewer)   

* iOS: [ObjectiveGit iOS Example](https://github.com/Raekye/ObjectiveGit-iOS-Example)

```
git submodule add https://github.com/libgit2/objective-git.git External/ObjectiveGit
如果之前配置过，直接更新：
git submodule update --init --recursive
```

1. `cd External/ObjectiveGit`，然后执行`./script/bootstrap`安装相关依赖.
1. 拖动 `ObjectiveGitFramework.xcodeproj` 文件 到iOS/OSX项目导航窗口 .
1. 在build Phases中配置APP的依赖，根据平台添加`ObjectiveGit-Mac` or `ObjectiveGit-iOS`.
1. APP通过连接器链接 `ObjectiveGit.framework`.
1. 在build setting中“Header Search Paths” (`HEADER_SEARCH_PATHS`)设置`libgit2`头文件在项目的路径，例如：`External/ObjectiveGit/External/libgit2/include`. 
1. Add a new "Copy Files" build phase, set the destination to "Frameworks" and add `ObjectiveGit.framework` to the list. This will package the framework with your application as an embedded private framework.
*  It's hard to tell the difference between the platforms, but the Mac framework is in `build/Debug` whereas the iOS framework is in `build/Debug-iphoneos`
1. Don't forget to `#import <ObjectiveGit/ObjectiveGit.h>` or `@import ObjectiveGit;` as you would with any other framework.

### 知识点
* 类变量关联.xib控件text值    
* 字体样式菜单来改变字体样式    
#### 在OSX中设置控件的Bindings代替IBOutlet  

先关联再使用属性依赖特性来同步数据

##### 类变量关联.xib控件text值  
[相关参考](http://stackoverflow.com/questions/8161012/referencing-bindings-in-connections-inspector)      
1. 选中NSTextField的bindings检查器面板   
2. 在`value`单元内设置bind to 的值，通过下拉框选中 `Delegate`      
3. Model key Path:输入类变量的名称。    
4. 切换到 NSTextField／Delegate的Connections检查器面板,就会看到已经建立了关联：     

##### 依赖属性
Foundation 框架提供的表示属性依赖的机制如下：
参考[属性的依赖](https://github.com/it-boyer/BookObjc/blob/master/publish/issue7/issue-7-3-DJBen.md#依赖的属性)
```objc
+ (NSSet *)keyPathsForValuesAffectingValueForKey:(NSString *)key

或
+ (NSSet *)keyPathsForValuesAffecting<键名>
```

将属性关联起来，这样就可以将类变量的值同步至UI控件中了：
```objc
+ (NSSet *)keyPathsForValuesAffectingValueForKey:(NSString *)key 
{
    NSSet *keySet = [NSSet setWithObjects:@"commit", nil];
    if ([key isEqualToString:@"messageTitle"] ||
        [key isEqualToString:@"messageDetails"] ||
        [key isEqualToString:@"author"] ||
        [key isEqualToString:@"date"])
    {
        return keySet;
    }
    return [super keyPathsForValuesAffectingValueForKey:key];
}
```


#### 实现字体样式菜单来改变字体样式   
1. 在xib中拖一个Object并设置为NSFontManager.h类的实现。   
2. 选中NSFontManager.h的connections面板，将Received Actions关联到对应的菜单项即可  
