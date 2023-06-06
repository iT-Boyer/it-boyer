---
title: iOS插件化开发之Small
date: 2018-04-11 16:42:17
updated: 2018-10-24 01:18:37
comments: true
tags: [架构]
categories:
- 学习笔记
keywords: 
permalink: 
---

{% github it-boyer SmallDemo 5931b43 width = 30% %}

[官网](http://code.wequick.net/Small/cn/home)
small是android与iOS平台比较出名的轻巧的跨平台插件化框架，也正是被这一点吸引，决定将small应用到集团内部的应用引擎模块化方案中，本篇博文主要讲述本人基于small在iOS平台实现的定制化APP方案（运营自由配置、自由组合、自动打包）~
[特性与功能](http://code.wequick.net/Small/cn/feature)
### 基于iOS组件化基础
iOS组件化基于[Cocoa Touch Framework][ctf]（以下简称CTF）通过[NSBundle][NSBundle]实现。
* CTF首次公开在WWDC2014，要求Xcode6 beta以上版本。
* CTF官方表示支持8.0以上系统，但在6.0、7.0上测试正常。
* 如果你的App包含了CTF，但是**Deployment Target** < 8.0，上传二进制文件到App Store时会报警中断。
受苹果官方限制，如果你的CTF没有签名，将无法实现代码级别更新。

Framework 模式无法上传到App Store。只能应用到企业版

[ctf]: https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/Framework.html#//apple_ref/doc/uid/TP40008195-CH56-SW1
[NSBundle]: https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle

### 使用Small模版新建l项目
Small提供了`Small-pods`模版，安装Xcode模版创建空白的Small项目。
1. **安装Xcode模板**
```bash
git clone https://github.com/wequick/Small.git
cd Small/iOS
cp -r Templates ~/Library/Developer/Xcode/Templates
```
2. **新建项目**
`File->New->Project...`，选择`Small-pods`模板
![Small iOS Template](https://camo.githubusercontent.com/25aac173476e3a5eecdf2853b0e233bf8179bece/687474703a2f2f636f64652e7765717569636b2e6e65742f6173736574732f696d616765732f736d616c6c2d696f732d74656d706c6174652e706e67)
* 库依赖配置文件`podfile`：
```js
platform :ios, '7.0'
use_frameworks!

target 'SmallAPP' do
    pod "Small", :git => 'https://github.com/wequick/Small.git'
end
```
* 路由文件`bundle.json`:
```json
{
    "version": "1.0.0",
    "bundles": [
        {
            "uri": "main",
            "pkg": "hsg.com.cn.SmallAPP.app.main"
        }
    ]
}

```
3. 安装pod依赖
```bash
cd [your-project-path]
pod install --no-repo-update
open *.xcworkspace
```
### 解读插件路由配置
[插件路由](http://code.wequick.net/Small/cn/router):为了方便插件之间的跨平台调用，Small 提供了 `bundle.json` 来完成插件路由。
bundle.json路由配置包括`version`:指定插件的版本号，`bundles`:插件注册的清单数组，其中插件清单的每个插件四个属性，来确定加载组件的方式：
`uri`：指定加载插件的跟路径
```
//获取控制器
let VC = Small.controller(forUri: "fixurl")
//将VC.view直接设置为window根视图
Small.openUri("fixurl", from: self)
```
`pkg`：配置要求新建的Framework命名时必须包含`.lib.`、`.app.`，因为在加载组件过程中用它来判断你插件的类型：
`rules`：规定页面的分发规则，可以通过rules来设置插件的多个入口，配和`uri`使用:`openuri(uri/ruleskey)`,当不配置rules时，默认通过`info.plist NSPrincipalClass`来加载组件`openuri(uri)`。
路由配置文件`bundle.json`部分内容如下：
```json
{
    "version": "1.0.0",    
    "bundles": [                
                {  
                   "uri": "lib.utils",                
                   "pkg": "com.example.small.lib.utils",
                   "rules": {  //会覆盖掉`Principal class`默认的启动页配置
                       "Storyboard": "storyboardName/controllerId",
                       "xib": "controllerName"
                   }               
                },                
                {                
                   "uri": "main",                
                   "pkg": "com.example.small.app.main"              
                }
               ]
               ....
}
```
small加载接口的相关方法
```
+ (void)openUri:(NSString *)uri fromView:(UIView *)view;
+ (void)openURL:(NSURL *)url fromView:(UIView *)view;

+ (void)openUri:(NSString *)uri fromController:(UIViewController *)controller;
+ (void)openURL:(NSURL *)url fromController:(UIViewController *)controller;

+ (UIViewController *)controllerForUri:(NSString *)uri;
+ (UIViewController *)controllerForURL:(NSURL *)url;
```
#### 支持Storyboard作为启动页的解析
根据`SMBundle`路由配置信息，通过`SMAppBundleLauncher`的实例方法`_controllerForBundle:`加载Framework，支持storyboard加载。
1. 路由`rules`字典
```
"rules":{
"":"Main/MainViewController"
}
```
空字串(`""`)的`value`值两种格式类型：

    `"$controllerName"`: `SMAppBundleLauncher`通过反射，初始化controller
    `"storyboardName/controllerId"`:`SMAppBundleLauncher`会识别找到storyboard在更具id初始化controller
最终可以`SMBundle`实例变量`target`中得到该key(`""`)的value值来定位插件包，在该过程通过对`SMBundle`的属性`bundle.queryParams`的处理，完成对插件对象的值传递
```objc
if ([bundle.target isEqualToString:@""]) {
        targetClazz = bundle.principalClass;
    } else {
        NSString *target = bundle.target;
        NSInteger index = [target rangeOfString:@"/"].location;
        if (index != NSNotFound) {
            // Storyboard: "$storyboardName/$controllerId"
            NSString *storyboardName = [target substringToIndex:index];
            targetBoard = [UIStoryboard storyboardWithName:storyboardName bundle:bundle];
            targetId = [target substringFromIndex:index + 1];
        } else {
            // Controller: "$controllerName"
            targetClazz = [bundle classNamed:target];
            if (targetClazz == nil && !SMStringHasSuffix(target, @"Controller")) {
            targetClazz = [bundle classNamed:[target stringByAppendingString:@"Controller"]];
        }
    }
}
UIViewController *controller = nil;
if (targetClazz != nil) {
    //尝试获取xib资源
    NSString *nibName = NSStringFromClass(targetClazz);
    NSString *nibPath = [bundle pathForResource:nibName ofType:@"nib"];
    if (nibPath != nil) {
        // 通过xib资源文件创建控制器实例
        controller = [[targetClazz alloc] initWithNibName:nibName bundle:bundle];
    } else {
        /// 通过反射类方式创建控制器实例
        controller = [[targetClazz alloc] init];
}
...
// Initialize controller parameters
if (bundle.queryParams != nil) {
    [controller setValuesForKeysWithDictionary:bundle.queryParams];
}
```
#### 参数传递
使用 [Query标准](https://en.wikipedia.org/wiki/Query_string)来传递参数，即在 `uri` 之后加上 `?` 再带上键值对，多个键值对用`&` 来分开。
1. 传值方式  `detail?id=1000&title=test`。
```
[Small openUri:@"detail?from=app.home" fromController:controller];
```
2. 接收解析为属性值
例如`DetailController`)定义两个属性，属性名称和`uri`键值名保持一致，因为是通过`setValuesForKeysWithDictionary`来给相应属性赋值。
```objc
// DetailController.h
@property (nonatomic, strong) NSString *id;

// DetailController.m
NSString *id = self.id;

样例
// Initialize controller parameters
if (bundle.queryParams != nil) {
    [controller setValuesForKeysWithDictionary:bundle.queryParams];
}
```

### 插件命名规则和入口设置
路由配置对插件包的命名有严格要求，`SMBundle`主要通过`pkg`名称包含`.app.`(模块库)/`.lib.`(工具库)来定位插件包的，否则全部默认加载bundle包。
**模块命名规范**
* `framework`编译成功后，名称跟`Product Name`一样命名规则:
```
xx_xx_lib_xx【com_example_small_lib_utils】
xx_xx_app_xx
xx_xx_xx_xx
```
注意lib、app这些对查找framework文件相当重要，这所以会有`_`，是small对`.`做了替换
```objc
NSString *bundlePath = nil;
NSString *bundleSuffix = @"bundle"; //默认
SMBundleType bundleType = SMBundleTypeAssets; 
if ([pkg rangeOfString:@".app."].location != NSNotFound
|| [pkg rangeOfString:@".lib."].location != NSNotFound) {
    bundleSuffix = @"framework";
    bundleType = SMBundleTypeApplication;
}
NSString *bundleName = [pkg stringByReplacingOccurrencesOfString:@"." withString:@"_"];
bundleName = [bundleName stringByAppendingFormat:@".%@", bundleSuffix];
```
**设置加载模块的入口类**
1. **info.plist**方式实现
在 `framework`模块工程的**info.plist**文件中添加`Principal class`字段：
```
<key>NSPrincipalClass</key>
<string>ESHomeController</string> //指定入口类名
```
2. **bundle.json**路由方式实现
通过设置**bundle.json**的`rules`字典，指定初始化库的入口
```
"rules": {  
               "": "默认入口类名"
    "/Storyboard": "storyboardName/controllerId",
           "/xib": "controllerName"
}  
```
> **bundle.json**中配置的入口，优先于`info.plist`中的`Principal class`的入口。

### 集成插件到主工程
1. **插件集成**
就是将`framework`添加到主工程，不能以Linked方式进行添加，使用`Build Phases`中的`Copy Bundle Resources` 选项，将`framework`拖动添加其中即可，这样可以完成对`framework`编译完后的拷贝.
2. **插件启动原理**
small框架会依次优先顺序检查`Documents/temp`（下载的zip）-->`/Documents/bundles`(存放Framework)-->`/iSmallApp.app/`(app根目录)，small规定插件`Framework`必须存放在这几个目录中，才能被small框架动态加载。
具体实现
```
NSString *bundleName = [pkg stringByReplacingOccurrencesOfString:@"." withString:@"_"];
bundleName = [bundleName stringByAppendingFormat:@".%@", bundleSuffix];
NSString *documentBundlesPath = [SMFileManager documentBundlesPath];
NSString *patchFilePath = [SMFileManager tempBundlePathForName:bundleName];
//沙盒中查找插件包，一旦发现，解压加载
if ([[NSFileManager defaultManager] fileExistsAtPath:patchFilePath]) {
    // Unzip
    NSString *unzipPath = documentBundlesPath;
    ZipArchive *zipArchive = [[ZipArchive alloc] init];
    [zipArchive UnzipOpenFile:patchFilePath];
    [zipArchive UnzipFileTo:unzipPath overWrite:YES];
    [zipArchive UnzipCloseFile];
    [[NSFileManager defaultManager] removeItemAtPath:patchFilePath error:nil];
}
NSString *patchPath = [documentBundlesPath stringByAppendingPathComponent:bundleName];
///主工程目录下查找
NSString *builtinPath = [[SMFileManager mainBundlesPath] stringByAppendingPathComponent:bundleName];
NSArray *bundlePaths = @[patchPath, builtinPath];
for (NSString *aBundlePath in bundlePaths) {
    if ([[NSFileManager defaultManager] fileExistsAtPath:aBundlePath]) {
        bundlePath = aBundlePath;
        break;
    }
}
```
3. **测试**
完成添加，进入测试。使用过程中，有可以模块更新代码后，主工程调用发现功能未更新，这时候需要清理工程，重新编译；或者修改编译包配置，从而时时更新。
![](https://img-blog.csdn.net/20160718094828722?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![](https://img-blog.csdn.net/20160718094836316?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### small的两种开发模式的demo
1. 使用者模式
使用场景：作为第三方集成到自己的项目，包含两个特殊的文件`podfile`和`Small-subprojects.rb`安装脚本文件。
**podfile**
```
platform :ios, '7.0'
use_frameworks!

target 'Sample' do
    pod "Small", :path => "../../"
end
```
 `Small-subprojects.rb`安装脚本文件
通过脚本来设置`build settings`中的**FRAMEWORK_SEARCH_PATHS**配置：
```
config.build_settings['FRAMEWORK_SEARCH_PATHS'] << "$(CONFIGURATION_BUILD_DIR)/**"
puts "Small: Add framework search paths for '#{dep.name}'"
```
2. 开发者模式
使用场景：需要对Small框架集成自己的功能需求时，可以使用该Demo快速部署对Small框架的开发环境
> 需要去除并行编译模式：`Edit Scheme...->Build->Build Options-> [ ] Parallelize Build`
    
> 各个组件需要签名后才支持代码级别更新。示例中更新例子为xib内容更新。<br/>

[使用Small创建iOS工程目录](https://blog.csdn.net/zhaowensky_126/article/details/51939230)
[Small UI route文档](https://github.com/wequick/Small/wiki/UI-route)
