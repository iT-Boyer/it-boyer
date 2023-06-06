---
title: 互动性的教学工具XCPlayground
date: 2017-03-01 17:27:16
updated: 2019-01-16 21:01:14
comments: true
tags: [playground]
categories:
- 学习笔记
keywords: 工具,语法,测试
permalink: 
---
Playground 展示语法和实时执行真实数据的特性，为编写方法和库接口提供了很好的机会，通过实时编译我们能了解语法、写出例子以及获得方法如何使用的说明，所有这些就如一个活的文档展示在眼前。
1. 演习框架API，了解框架结构
[SceneKitMac.playground](https://github.com/objcio/PersonalSwiftPlaygrounds)
是一个功能完备带动画的 3D 场景。你需要打开 Assistant Editor (在菜单上依次点击 View | Assistant Editor | Show Assistant Editor)，3D 效果和动画将会被自动渲染。这不需要编译循环，而且任何的改动，比如改变颜色、几何形状、亮度等，都能实时反映出来。使用它能在一个交互例子中很好的记录和介绍如何使用SceneKit框架。
2. 测试驱动开发 
我们可以验证一个方法的执行是否正确，甚至在加载到 playground 的时候就能判断方法是否被正确解析。不难想象我们也可以在 playground 里添加断言，以及创建真正的单元测试。或者更进一步，创建出符合条件的测试，从而在你打字时就实现测试驱动开发。

## Sources
打开 Project Navigator (⌘1) 并展开 Playground 文件，你就能看到"Sources"路径。
放到此目录下的源文件会被编译成模块(module)并自动导入到 Playground 中，并且这个编译只会进行一次(或者我们对该目录下的文件进行修改的时候)，而非每次你敲入一个字母的时候就编译一次。 这将会大大提高代码执行的效率。
>注意：由于此目录下的文件都是被编译成模块导入的，只有被设置成 public 的类型，属性或方法才能在 Playground 中使用。


## 导入Frameworks
如果想要导入外部 framework，创建一个 Xcode Workspace 包含了 framework 项目和你的 Playground。在 Build 之后，就可以通过常规的import命令导入对应的包。

### 手动配置cocoa touch Framework来桥接playground
在Playgroud中使用个人项目中的类相关方法，需要借助于Custom Frameworks桥接
注：.swift的文件中的方法必须是public修饰。
参考文档：`Playground help -> Importing Custom Frameworks into a Playground`
1. 导入个人项目文件，需要借助`cocoa touch Framework`桥接`playground`
2. 需要`workspace`来管理`Framework`项目和`playground`文件，典型例子：pod项目都是用workspace来管理多个项目。
3. 把个人项目的`swift文件`关联到`Cocoa touch Framework`项目的`target`中：
    详细设置：选中target -> build phases -> compiles sources ->点击 + 加号，选中原项目中的swift
4. 在`build`选项中选中Framework的scheme进行编译 ,要保证framework的target配置：`build setting -> build active architecture Only ->debug`选项设置为`YES`
5. 打开playground文件 import Framework名称，此时即可使用Framework中的提供的public API方法了。
Workspace相关设置，build生成的目录：xcode偏好设置要和项目中的workspace中设置要保持一致.
1. xcode的偏好设置中 ->Locations -> Locations ->点击打开 Advanced...在弹出框中设置Unique选项.
2. 在workspace中选中菜单 File -> workspace settings... -> 在弹出框中设置为Unique选项.

### 导入cocopads管理的依赖库
xcode7.3.1和cocoapods1.0版本导致playground无法import相关动态库
解决办法：http://stackoverflow.com/questions/38216238/xcode-playground-with-cocoapods#
{% codeblock  lang:bash %}
#在写入磁盘之前，修改一些工程的配置:
post_install do |installer|
    installer.pods_project.targets.each do |target|
        if target.name != 'CocoaAsyncSocket'
            #playground相关配置，会导致'GCDAsyncSocket.h' file not found
            target.build_configurations.each do |config|
                config.build_settings['CONFIGURATION_BUILD_DIR'] = '$PODS_CONFIGURATION_BUILD_DIR'

                #Use Legacy Swift Language Version” (SWIFT_VERSION):
                #   https://github.com/CocoaPods/CocoaPods/issues/5864#issuecomment-247109685
                puts "SWIFT_VERSIION:"
                config.build_settings['SWIFT_VERSION'] = "3.0.1"
                puts config.build_settings['SWIFT_VERSION']
            end
        else
            #输出操作
            puts "以下不能在playground中使用的库名："
            puts target.name
        end
    end
end
{% endcodeblock %}

## Playground沙盒Resources
Playgrounds 有两个与相关的Resources关联起来：一个是每一个独立的 playground 本地的，另一个则是 playground 之间共享的。在你的实验过程中，Playgrounds 能够支持 XML，JSON 数据，XIB，和图像文件。这也增加了其使用可用性。


## 本地 bundle访问本地资源
Resources 文件夹, 与 Sources 文件夹一样在 Playground 的包路径中, 通过 Project Navigator 就可见了——只需要简单的拖拽图像和数据文件，就可以在 Playground 中使用了。对应的内容在 main bundle 中也是可见的。比如，我们可以像这样非常快捷的加载一个包含天气数据的 JSON 文件：
{% codeblock lang:swift %}
let jsonPath = NSBundle.mainBundle().bundlePath.stringByAppendingPathComponent("weather.json")
if let
jsonData = NSData(contentsOfFile: jsonPath),
json = NSJSONSerialization.JSONObjectWithData(jsonData, options: nil, error: nil) as? [String: AnyObject] 
{
    // ...
}
{% endcodeblock %}


## 共享 访问Documents共享目录

"共享 Playground 数据"的内容在你的"Documents"文件夹路径下，也同样对于你创建的任何 Playground 都可见。我们通过XCPSharedDataDirectoryPath常量来访问该共享文件夹。

如果你自习想尝试，需要在 "~/Documents/Shared Playground Data" 下简历一个文件夹。 这里我们尝试载入一个名字叫做 "image.png" 的图片文件:

{% codeblock lang:swift %}
let sharedImagePath = XCPSharedDataDirectoryPath.stringByAppendingPathComponent("image.png")
if let image = UIImage(contentsOfFile: sharedImagePath) {
    // ...
}
{% endcodeblock %}

## PlaygroundSupport

### liveView视图代理
实现在playground中实现UI显示及交互操作
liveView定义：
{% codeblock lang:swift %}
public var liveView: XCPlaygroundLiveViewable?
{% endcodeblock %}
遵循了`XCPlaygroundLiveViewable`协议即可在playground中可视化显示：
1. 在iOS 和 tvOS中`UIView` and `UIViewController`遵循该协议
2. 在OS X中`NSView` and `NSViewController`遵循该协议
3. 用户自定类型，须遵守XCPlaygroundLiveViewable协议
总之，只要遵守改协议并实现代理方法，都可以在playground中可视化显示。
用法：
{% codeblock lang:swift %}
PlaygroundPage.current.liveView = UIView()／NSViewController()
{% endcodeblock %}

### 捕获值（XCPCaptureValue在XCPlayground中过时）
[冒泡排序可视化预览](http://swifter.tips/playground-capture/)
{% codeblock XCPlayground Module lang:swift %}
/// This function has been deprecated.
@available(*, deprecated)
public func XCPCaptureValue<T>(identifier: String, value: T)
{% endcodeblock %}

简介：可以多次调用该方法来做图，相同的 identifier 的数据将会出现在同一张图上，而 value 将根据输入的次序进行排列,将一组数据轻而易举地绘制到时间轴上，从而让我们能看到每一步的结果。这不仅对我们直观且及时地了解算法内部的变化很有帮助，也会是教学或者演示时候的神兵利器。
1. 使用：导入框架`import XCPlayground`
2. 扩展：XCPCaptureValue 的数据输入是任意类型的，所以不论是传什么进去都是可以表示的。它们将以 QuickLook 预览的方式被表现出来，一些像 UIImage，UIColor 或者 UIBezierPath 这样的类型已经实现了 QuickLook。当然对于那些没有实现快速预览的 NSObject 子类，也可以通过重写

一个 Playground 通常立即显示简单表达式的结果。数组，字符串，数字等等，会在结果面板把计算后的结果显示出来。那么，随着时间改变的值是如何处理的呢？

通过使用 XCPCaptureValue() 函数，我们可以随着一系列的迭代建立一个变动值的图。回到我们上面提到的天气例子，让我们来看看按小时计的温度数据，使用 XCPCaptureValue 来在辅助编辑界面以时间线的方式显示 温度的值：

{% codeblock lang:swift %}
import XCPlayground

for forecast in forecasts 
{
    if let tempString = forecast["temp"]?["english"] as? String, temperature = tempString.toInt()
    {
        XCPCaptureValue("Temperature", temperature)
    }
}
{% endcodeblock %}

另一种可选的方式是, 选择 Editor → Show Result For Current Line 就会捕获当前线的数值并且直接以图表的形势显示在 Playground 流中：
    


### 异步执行（Asynchronous Execution）
不同于大部分 Swift 代码，是作为框架或者应用的一部分，Playgrounds 被当做是 高级代码。Playground 中的高级代码是按照指令接着指令的顺序从上到下执行的。
这种无容器风格的代码执行提供了立即反馈，但是存在着一个问题：在执行到了 Playground 底部后，会立即停止。网络请求，计时器，以及长时间运行的后台队列都会在提供反馈成功或者失败之前被立即终止。
`PlaygroundSupport` 模块包含一个能够延长该过程的函数：
{% codeblock lang:swift %}
public var needsIndefiniteExecution: Bool
{% endcodeblock %}
1. 开启异步：默认值为`false`,当使用liveView代理时，会自动设置为`true`。当为`true`时，在高级代码执行完成后，会告诉Xcode继续运行Playground页面。为`false`时，当代码执行完直接终止。
3. 手动终止：还可以使用`PlaygroundPage.finishExecution()`手动终止正在运行的Playground。

{% codeblock lang:swift %}
import PlaygroundSupport

PlaygroundPage.current.needsIndefiniteExecution = true

let url = NSURL(string: "http://httpbin.org/image/png")!
let task = NSURLSession.sharedSession().dataTaskWithURL(url) {
    data, _, _ in
        let image = UIImage(data: data)
        // ...
    
        //手动终止
        PlaygroundPage.current.currentPage.finishExecution()
}
task.resume()
{% endcodeblock %}

## 支持Markdown格式的文档
[官方文档](https://developer.apple.com/library/content/documentation/Xcode/Reference/xcode_markup_formatting_ref/)
除了实验用途，Playgrounds 在展示 Swift 语言的工具和框架中也一样强大。特别文档部分可以作为丰富格式的方式展示出来，以提供对于代码的清晰解释从而展示某个技术或者正确使用某个 Library 的方式。

不同于[Swift代码中的注释文档语法](http://nshipster.cn/swift-documentation/), `Swift Playgrounds` 使用 `Markdown` 来显示多格式的文档。
例如：
`//:`：可以指定单行文本说明
`/*: Markdown格式内容... */`：可以用`Markdown`格式来显示丰富的文档内容

`xcode`切换备注以`Markdown`格式显示：
1. 选择`Editor → Show Rendered Markup` 菜单
2. 在 `File Inspector (⌘⌥1)` 选中 `Render Documentation` 复选框。
>在xcode8中打开之前版本时，菜单上的`Show Rendered Markup`会显示为`Upgrade playgound...`升级菜单项，这样就需要先点击升级菜单项之后，才能激活上述（1）（2）设置项。

{% codeblock lang:swift %}
//: This line will have **bold** and *italic* text.

/*:
## Headers of All Sizes

### Lists of Links

- [NSHipster](http://nshipster.com)
- [ASCIIwwdc](http://asciiwwdc.com)
- [SwiftDoc](http://swiftdoc.org)

### Images, Too

![Remote Image](http://nshipster.s3.amazonaws.com/alert.gif)
![Local Image](bomb.gif) 

*Images in the Resources directory can be referenced locally*
*/

{% endcodeblock %}




Playgrounds 提供了一个我们关于分享和学习 OS X 和 iOS 相关工具的方式的重大改变。Playground 可以展示每一个特性，并且为将来的用户探索和发现你创建的库提供了空间。丢掉你的静态README.md， 换成可互动的README.playground吧，再玩起来！




