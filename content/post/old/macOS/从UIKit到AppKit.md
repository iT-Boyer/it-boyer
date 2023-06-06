---
title: 从UIKit到AppKit
date: 2017-03-03 17:55:16
updated: 2019-01-16 21:01:14
comments: true
tags: [macos]
categories: 
- 解决方案
keywords: 语法,UI,混编
permalink: 
---
## 不同点
### NSWindowController
在`Mac`上应用都支持多窗口（`NSWindowController`），`AppKit` 中都有 `NSWindowController`担当着类似在`iOS`中的`view controller`处理的任务。
>`window`在`iOS`占据整个屏幕，几乎不怎么不用。
### NSViewController
`AppKit` 中的 `NSViewController`默认不支持交互，缺少生命周期相关方法和`UIKit`中熟悉的特性。
但在OS X 10.10 Yosemite之后，`NSViewController`改进很多，默认支持交互中的响应链。

### NSWindow 和 UIWindow
在`UIKit`中`UIWindow`是一个 `view` 的子类.
在`AppKit`中`NSWindow`用 `contentView` 属性持有一个指向其顶层 `view` 的引用。

## 响应者链（responder chain）
如果你在为 OS X 10.9 或者更低版本的系统开发，请注意在默认情况下`view controller` 并不是响应者链的一环。相反，事件会沿着视图树向上传递然后直接到达 `window` 和 `window` `controller`。在这种情况下，如果你想在 `view controller` 处理事件，你需要手动把它添加到响应者链中。

### Target-Action消息传递方式
`Target-Action` 是回应 `UI 事件`时典型的消息传递方式。`iOS` 上的 `UIControl` 和 `Mac` 上的 `NSControl/NSCell` 都支持这个机制。
`Target-Action` 在消息的发送者和接收者之间建立了一个松散的关系。消息的接收者不知道发送者，甚至消息的发送者也不知道消息的接收者会是什么。如果 `target` 是 `nil`，`action` 会在响应链 (responder chain) 中被传递下去，直到找到一个响应它的对象。

#### 传递机制的局限
基于 `target-action` 传递机制的一个局限是，发送的消息不能携带自定义的信息：
1. 在`iOS` 中，可以选择性的把发送者和触发 `action` 的事件作为参数。
2. 在 `Mac` 平台上 `action` 方法的第一个参数永远是发送者，否则将不视为无效方法。
在`AppKit`唯一有效的`action` 方法声明方式：
{% codeblock lang:objc %}
- (void)performAction:(id)sender;
{% endcodeblock %}

#### 控件关联Action事件的区别
`iOS` 上的 `UIControl` 和 `Mac` 上的 `NSControl/NSCell` 都支持这个机制。
1. 在 `iOS` 中，每个控件可以通过`addTarget:action:forControlEvents:`和多个 `target-action` 关联。
2. 在`AppKit`中通常一个`控件`仅对应一个 `target-action`对。

## View视图系统
因为历史遗留问题，`Mac` 的视图系统和 `iOS`的视图系统有很大区别。
1. 绘图处理器差异
`iOS`由 `Core Animation layer` 驱动，使用`GPU`处理。
`AppKit`视图系统相关的任务主要靠 `CPU` 处理，因为在`AppKit`设计之初，`GPU`还没有诞生。
Mac开发参考资料：
[Introduction to View Programming Guide for Cocoa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CocoaViewsGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40002978)
[WWDC session：Layer-Backed Views: AppKit + Core Animation](https://developer.apple.com/videos/wwdc/2012/#217)
[Optimizing Drawing and Scrolling](https://developer.apple.com/videos/wwdc/2013/#215)

### Layer-Backed View: iOS反哺AppKit层支持视图 
1. `iOS`反哺`AppKit`
默认情况下，`AppKit` 的 `view` 不是由 `Core Animation layer` 驱动的；`AppKit` 整合 `layer-backing` 是 `iOS` 反哺的结果。

#### layer backing启用／禁用:wantsLayer
`AppKit` 区分 `layer-backed view` 和 `layer-hosting view`，可以在每个视图树的根节点启用或者禁用 `layer backing`。
启用 `layer backing`
1. 方法一：把窗口的 `contentView` 的 `wantsLayer` 属性设置为 `YES`
2. 方法二：在 `Interface Builder` 的 `View Effects Inspector` 面板完成
这会导致 `window` 的视图树中所有的 `view` 都启用 `layer backing`，这样就没必要反复设置每个 `view` 的 `wantsLayer` 属性了。

#### 修改layer属性
在`AppKit`上开启`layer backing`之后，`layer`的拥有者是`AppKit`，这就意味着不能直接编辑 `layer`的属性。
在 `iOS` 上可以直接编辑：
{% codeblock lang:objc %}
self.layer.backgroundColor = [UIColor redColor].CGColor;
{% endcodeblock %}
在 `AppKit`编辑`layer`属性需要以下步骤：
1. 重写 `NSView` 的 `wantsUpdateLayer` 方法并返回 `YES`,这能让你可以改变 `layer` 的属性。
这样以来，在`view` 更新周期中，将不会再调用 `view` 的 `drawRect:` 方法。取而代之，调用`updateLayer`方法来更新`Layer`。
举个例子，用这方法去实现一个非常简单的有纯色背景的 `view`（没错，`NSView` 没有`backgroundColor` 属性）：
这个例子的前提是这个 `view` 的`父 view` 已经为其视图树启用了 `layer backing`。
另一种可行的实现则只需要重写 `drawRect:` 方法并在其中绘制背景颜色。
{% codeblock lang:objc %}
@interface ColoredView: NSView
@property (nonatomic) NSColor *backgroundColor;
@end

@implementation ColoredView
- (BOOL)wantsUpdateLayer
{
    return YES;
}

- (void)updateLayer
{
    self.layer.backgroundColor = self.backgroundColor.CGColor;
}

- (void)setBackgroundColor:(NSColor *)backgroundColor
{
    _backgroundColor = backgroundColor;
    [self setNeedsDisplay:YES];
}
@end
{% endcodeblock %}

#### 合并 Layer
当APP启动过多的`layer-backed view` 会带来巨大的内存消耗（每一个 `layer` 有其自己的 `backing store`，还有可能和其他 `view `的 `backing store` 重叠）而且会带来潜在的合成这些 `layer` 的消耗。

##### canDrawSubviewsIntoLayer合并Layer
从 OS X 10.9 开始，如果不单独对一个 `view` 中的子 `view` 做动画，可以通过设置 `canDrawSubviewsIntoLayer` 属性来让 `AppKit` 合并一个`视图树`中所有 `layer` 的内容到一个共有的 `layer`。

##### 隐式layer-backed合并Layer
所有隐式 `layer-backed` 的`子 view`（比如，没有显式地对这些`子 view` 设置 `wantsLayer = YES`）现在将会被绘制到同一个 `layer` 中。不过`wantsLayer` 设置为 `YES` 的`子 view` 仍然持有它们自己的 `backing layer`， 而且不管 `wantsUpdateLayer` 返回什么，它们的 `drawRect:` 方法仍然会被调用。

#### Layer 重绘策略

##### layer-backed view 默认的自动重绘策略
`layer-backed view` 会默认设置重绘策略为 `NSViewLayerContentsRedrawDuringViewResize`。在行为上，这个非 `layer-backed view` 是类似的，不过如果动画的每一帧都引入一个绘制步骤的话可能会对动画的性能造成不利影响。

##### 设置layer-backed view手动重绘策略
1. 设置手动重绘策略：把 `layerContentsRedrawPolicy` 属性设置为 `NSViewLayerContentsRedrawOnSetNeedsDisplay` 
2. 实现手动重绘操作：调用 `-setNeedsDisplay:`方法来触发重绘操作
这样便由你来决定 `layer` 的内容何时需要重绘。帧的改变将不再自动触发重绘。

##### 设置view的属性来重绘Layer
1. view中`layerContentsPlacement`属性：等价`layer`中的 `contentGravity` 属性。
这个属性允许你指定在调整大小的时候当前的 `layer` 内容该怎么映射到 `layer` 上。


### Layer-Hosting View:使用 Core Animation layer
`layer-hosting view` 是视图树中的叶子节点，使用这种模式可以对`layer` 及其`子 layer` 做任何操作，代价是你再也不能给该 `view` 添加任何`子 view`。
#### 创建
1. 为 `view` 的 `layer` 属性分配一个 `layer 对象`，
2. 设置`wantsLayer` 为 `YES`
这些步骤的顺序是非常关键：
{% codeblock lang:swift %}
- (instancetype)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (self) 
    {
        self.layer = [[CALayer alloc] init];
        self.wantsLayer = YES;
    }
}
{% endcodeblock %}
在你设置了自定义的 `layer` 之后，再设置 `wantsLayer` 是非常重要的。

### 其他与 View 相关的陷阱

#### 坐标系统原点设置左下／左上角
通过重写`isFlipped` 并返回 `YES` 来恢复到你熟悉的左上角。

#### View背景颜色属性drawsBackground
由于 `AppKit` 中的 `view` 没有背景颜色属性可以让你直接设置为 `[NSColor clearColor]` 来让其变得透明，许多 `NSView` 的子类比如 `NSTextView` 和 `NSScrollView` 开放了一个 `drawsBackground` 属性，如果你想让这一类 `view` 透明，你必须设置该属性为 `NO`。

#### 设置光标追踪区域 
为了能接收光标进出一个 view 或者在 view 里面移动的事件，你需要创建一个追踪区域。你可以在 `NSView` 中指定的 `updateTrackingAreas` 方法中来做这件事情。一个通用的写法看起来是这样子的：
{% codeblock lang:swift %}
- (void)updateTrackingAreas
{
    [self removeTrackingArea:self.trackingArea];
    self.trackingArea = [[NSTrackingArea alloc] initWithRect:CGRectZero 
                                                     options:NSTrackingMouseEnteredAndExited|NSTrackingInVisibleRect|NSTrackingActiveInActiveApp
                                                       owner:self 
                                                    userInfo:nil];
    [self addTrackingArea:self.trackingArea];
}
{% endcodeblock %}

#### NSCell困惑
`AppKit` 的控件之前是由 `NSCell` 的子类驱动的，可以被所有同类型的控件重用。
`AppKit` 最初区分 `view` 和 `cell` 是为了节省资源 - `view` 可以把所有的绘制工作代理给更轻量级的可以被所有同类型的 `view` 重用的 `cell` 对象。
不要混淆这些 `cell` 和 `UIKit` 里 `table view` 的 `cell` 及 `collection view` 的 `cell`。

##### 自定义一个按钮控件
Apple 正在一步步地抛弃这样的实现方法了，但是你还是会时不时碰到这样的问题。
举个例子，如果你想创建一个自定义的按钮，
1. 首先要继承 `NSButton` 和 `NSButtonCell`
2. 然后在这个 `cell 子类`里面进行你自定义的绘制，
3. 然后通过重写 `+[NSControl cellClass] `方法告诉自定义按钮使用你的 `cell 子类`

#### 获取 Core Graphics 上下文
最后，如果你想知道在你自己的 `drawRect:` 方法里怎么获取当前的 `Core Graphics` 上下文，答案是 `NSGraphicsContext` 的 `graphicsPort` 属性。详细内容请查看 [Cocoa Drawing Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CocoaDrawingGuide/)。

## 动画
如果你的 `view` 不是由 `layer` 驱动的，那你的动画自然是完全由 `CPU` 处理，这意味着动画的每一步都必须相应地绘制到 `window-backing store` 上。

### 对 layer-backed view做动画
正如上面说的，在 `AppKit` 中,这些 `layer` 由 `AppKit` 管理，你不应该修改 `layer-backed view` 中的 `layer`。 

#### 几何属性
与`iOS` 相反，`view` 的几何属性并不仅仅是对应的 `layer` 的几何属性的映射，但 `AppKit` 却会把 `view` 内部的几何属性同步到 `layer`。

#### 的animator proxy动画
{% codeblock lang:objc %}
view.animator.alphaValue = .5;
{% endcodeblock %}
在幕后，这句代码会启用 `layer` 的隐式动画，设置其透明度，然后再次禁用 `layer` 的隐式动画。

#### NSAnimationContext动画
1. 结束回调
{% codeblock lang:objc %}
[NSAnimationContext runAnimationGroup:^(NSAnimationContext *context){
    
    //持续时间
    context.duration = 1;  
    //缓动类型
    context.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn];
    view.animator.alphaValue = .5;

} completionHandler:^{
    // ...
}]; 
{% endcodeblock %}
2. 无结束回调
{% codeblock lang:objc %}
[NSAnimationContext currentContext].duration = 1;
view.animator.alphaValue = .5; 
{% endcodeblock %}

#### 启用隐式动画
{% codeblock lang:objc %}
[NSAnimationContext currentContext].allowsImplicitAnimations = YES;
view.alphaValue = .5;
{% endcodeblock %}

#### CAAnimations控制动画
使用 `CAAnimation` 实例更全面地控制动画。和 `iOS` 相反，你不能直接把它们加到 `layer` 上（因为 `layer` 不应该由你来修改），不过你可以使用 `NSAnimatablePropertyContainer` 协议中定义的 `API`，`NSView` 和 `NSWindow` 已经实现了该协议。
{% codeblock lang:objc %}
CAKeyframeAnimation *animation = [CAKeyframeAnimation animation];
animation.values = @[@1, @.9, @.8, @.7, @.6];
view.animations = @{@"alphaValue": animation};
view.animator.alphaValue = .5;
{% endcodeblock %}

##### 帧动画
对于帧动画来说，把 `view` 的 `layerContentsRedrawPolicy` 设置为 `NSViewLayerContentsRedrawOnSetNeedsDisplay` 是非常重要的，不然的话 `view` 的内容在每一帧都会被重绘。

很遗憾，`NSView` 没有开放 `Core Animation layer` 所有可以进行动画的属性，`transform` 是其中最重要的例子。看看 Jonathan Willings 的这篇文章，它描述了你可以如何解决这些限制。不过注意，文章中的解决方案是不受官方支持的。

上面提到的所有东西都适用于 `layer-backed view`。对于 l`ayer-hosting view` 来说，你可以直接对 `view` 的 `layer` 或者`子 layer` 使用 `CAAnimations`，因为你拥有它们的控制权。


### 文字系统
有了 `TextKit`，`iOS 7` 终于有了和 `Mac` 上早就有了的 `Cocoa Text System` 等效的东西。但 Apple 并不仅仅是把文字系统从 Mac 上转移到 `iOS`；相反，Apple 对其做了些显著的改变。
举个例子，`AppKit` 开放 `NSTypesetter` 和 `NSGlyphGenerator`，你可以通过继承这两者来自定义它们的一些特性。`iOS` 并不开放这些类，但是你可以通过 `NSLayoutManagerDelegate` 协议达到定制的目的。
总体来说，两个平台的文字系统还是非常相似的，所有你在 `iOS` 上能做的在 `Mac` 上都可以做（甚至更多），但对于一些东西，你必须从不同的地方寻找合适的方法实现。

### 沙盒
符合沙盒机制的 `Mac 应用`才能通过 `Mac App Store` 销售。然而，我们已经习惯了沙盒机制还没出现之前的 `Mac` 开发环境，所以有时候会忽视一些你想要实现的功能会和沙盒的限制出现冲突。
管理Mac应用对沙盒支持：

## 独有特性
有很多事情你只能在 Mac 上做，这主要是因为它不同的交互模型和它更为宽松的安全策略。在本期话题中，我们有一些文章深入探讨了其中的一些内容：[进程间通讯](http://objccn.io/issue-14-4/)，[使 Mac 应用脚本化](http://objccn.io/issue-14-1/),[在沙盒中脚本化其他应用](http://objccn.io/issue-14-2/) , [为你的应用构建插件](http://objccn.io/issue-14-3/)。
当然，这只是 Mac 独有特性中很小的一部分，但这给了你一个很好的视角看待 iOS 8 从头开始打造其可扩展性和 app 间通讯。最后，还有很多东西等待你去探索：Drag and Drop，Printing，Bindings，OpenCL 等等，这里仅仅是举几个例子。








