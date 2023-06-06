---
title: 开发Mac上的App插件
date: 2017-02-07 12:38:58
updated: 2019-01-16 21:01:14
comments: true
tags:  [macOS]
categories: 
- 解决方案
keywords: 
permalink: 
---
在以前的 OS X 系统中，给你的 App 在运行时动态载入可执行代码比较困难。现在可以通过`NSBundle`和 `plug-ins`插件,可以很方便的向原有APP中添加新功能点。

目的：在一个修改过的 TextEdit 里面加入加载 bundle 的功能

## 包 (Bundles) 和接口 (Interfaces)
如果你打开 Xcode8 创建一个新项目，会看见 OS X 所有可以编写APP插件的模版，例如： `Screen Savers` 到 `Image Units`等。
在`Framework & Library`中的 `Bundle` 条目。我会在今天探索一个非常简单的的项目，那就是在一个修改过的 TextEdit 里面加入加载 bundle 的功能。
`bundle 模版`项目与APP项目比较：
1. 一个 `Contents` 目录，里面包含了 `Info.plist` 和 `Resource` 目录。
2. 如果你在你的项目下加入了新的类，你可以看见包含一个可执行文件的 `MacOS` 目录。
3. `Bundle` 工程里缺少的一个东西是 `main()` 函数。它是被宿主`App` 调用执行的。

## 为 TextEdit 加入 Plugin 支持
两种插件的方式:
    第一个:用最少的工作来为你的 app 加入插件支持，希望让你知道实现这个有多简单。
    第二个:技术有点复杂，它展现来一个为你的 app 加入插件的合理的方式，这可以使你不会在未来陷入到被锁死在某一种实现的窘境中。
本文章的项目文件仍然会放在 [GitHub](https://github.com/objcio/issue-14-plugins) 供大家参考。

### 在 TextEdit 中扫描 Bundle
请打开 "01 TextEdit" 目录下面的 `TextEdit.xcodeproj` 工程，同时浏览它里面包含的代码。
`TextEdit` 里面有三个简单的组成部分：扫描 `bundle`，加载 `bundle`，调用 `bundle 的 UI`
`loadPlugins` 方法：
打开 `Controller.m`，你可以看见 `-(void)loadPlugins` 方法 (它在 `applicationDidFinishLaunching:` 中被调用)。
1. 扩展插件菜单：在界面菜单右侧加入了一个新的 `NSMenuItem`，为调用插件提供一个入口（通常你会在 `MainMenu.xib` 做这件事情并且链接 `outlets`，但是我们这次偷下懒）。
2. 扫描插件目录：获得插件目录（在 `~/Library/Application Support/Text Edit/Plug-Ins/` ）下，并且扫描这个目录。
{% codeblock lang:objc %}
NSString *pluginsFolder = [self pluginsFolder];
NSFileManager *fm = [NSFileManager defaultManager];
NSError *outErr;
for (NSString *item in [fm contentsOfDirectoryAtPath:pluginsFolder error:&outErr]) 
{
    if (![item hasSuffix:@".bundle"])
    {
        continue;
    }
    NSString *bundlePath = [pluginsFolder stringByAppendingPathComponent:item];
    NSBundle *b = [NSBundle bundleWithPath:bundlePath];
    if (!b) 
    {
        NSLog(@"Could not make a bundle from %@", bundlePath);
        continue;
    }
    //获取实现插件代理协议方法的类
    id <TextEditPlugin> plugin = [[b principalClass] new];
    NSMenuItem *item = [pluginsMenu addItemWithTitle:[plugin menuItemTitle] action:@selector(pluginMenuItemCalledAction:) keyEquivalent:@""];
    [item setRepresentedObject:plugin];
}
{% endcodeblock %}
> 注：扫描插件目录，确保得到的是一个 `.bundle` 文件，然后用 `NSBundle` 载入你找到的 `bundle` 并且实例化里面的类。

### 插件代理
你会注意到一个 `TextEditPlugin` 的 `protocol` 的引用。在 `TextEditMisc.h` 能找它的定义:
{% codeblock 声明代理协议 lang:objc %}
@protocol TextEditPlugin <NSObject>
- (NSString*)menuItemTitle;
- (void)actionCalledWithTextView:(NSTextView*)textView inDocument:(id)document;
@end
{% endcodeblock %}
这说明你实例化的类需要响应这两个方法。你可以验证这个类是否响应这两个方法。

### NSPrincipalClass键:值--实现插件代理协议方法的类名称
在 `bundle` 里面调用的 `principalClass` 方法是什么呢？
当你创建一个 `Bundle` 的时候，你可以在里面创建一个或者多个类，同时你需要让 `TextEdit` 知道哪一个类需要被实例化。为了帮助宿主 App 调用，你可以在 `Info.plist` 文件加入一个 `NSPrincipalClass` 的键，同时设置它的值为实现插件方法的类的名字。你可以用 `[NSBundle principalClass]` 方便地从 `NSPrincipalClass` 的值里面寻找并创建这个类。

### 添加扩展插件菜单的响应事件
在 `Plug-Ins` 菜单加入一个新的按钮，设置 `action` 为 `pluginMenuItemCalledAction:`，并且设置它表示你已经实例化的对象。
> 如果在 `menu item` 里面没有设置一个`target`，即目标是`nil`，那么它会寻找响应链，来寻找第一个实现 `pluginMenuItemCalledAction:` 方法的对象。如果它找不到，那么这个菜单选项将会不能用。
举一个例子，实现 `pluginMenuItemCalledAction` 的最好的地方是在 `Document` 的 `window controller` 类中。打开 `DocumentWindowController.m`，然后定位到`pluginMenuItemCalledAction`
{% codeblock lang:objc %}
- (void)pluginMenuItemCalledAction:(id)sender 
{
    id <TextEditPlugin>plugin = [sender representedObject];
    [plugin actionCalledWithTextView:[self firstTextView] inDocument:[self document]];
}
{% endcodeblock %}

代码本身很清晰，搜集插件实例，调用 `actionCalledWithTextView:inDocument:` 方法（被定义在 `protocol` 里面的），运行你插件里面的代码。

## 制作插件
1. 新建模版项目
打开 "01 MarkYellow" 工程看一下。这是一个 Xcode (通过`OS X ▸ Framework & Library ▸ Bundle template` 建立) 的标准工程，里面只添加了一个类：`TEMarkYellow`。
2. 设置NSPrincipalClass键值
如果你打开` MarkYellow-Info.plist`，你可以看到 `NSPrincipalClass` 的值设置成了上面提到的 `TEMarkYellow`。
3. 实现协议代理
接着，打开 `TEMarkYellow.m`，你将会看见定义在协议里面的方法。
    * 第一个方法（`menuItemTitle`）返回插件的名字，最为入口名显示在 `menu` 里面。
    * 第二个方法 (`actionCalledWithTextView:inDocument:`)，把所有选中的文字变成黄色的背景。
{% codeblock 改变字体颜色 lang:objc %}
- (void)actionCalledWithTextView:(NSTextView*)textView inDocument:(id)document 
{
    if ([textView selectedRange].length) 
    {
        NSMutableAttributedString *ats = [[[textView textStorage] attributedSubstringFromRange:[textView selectedRange]] mutableCopy];
        [ats addAttribute:NSBackgroundColorAttributeName value:[NSColor yellowColor] range:NSMakeRange(0, [ats length])];
        //  先测试text view是否能改变文字内容，这样可以自动做正确的撤销操作。
        By asking the text view if you can change the text first, it will automatically do the right thing to enable undoing of attribute changes
        if ([textView shouldChangeTextInRange:[textView selectedRange] replacementString:[ats string]])
        {
            [[textView textStorage] replaceCharactersInRange:[textView selectedRange] withAttributedString:ats];
            [textView didChangeText];
        }
    }
}
{% endcodeblock %}

## 集成插件
运行 `TextEdit` （它会创建`Plug-Ins`目录），然后构建 `MarkYellow` 工程。把 `MarkYellow.bundle` 丢到你的 `~/Library/Application Support/Text Edit/Plug-Ins/` 目录下面，重启你的 `TextEdit` 应用。
一切看起来都很好，扫描，加载，插入一个菜单，然后，当你使用菜单项的时候，传递到参数到插件里面。试一试，点击 `Plug-Ins ▸ Mark Selected Text Yellow`，选择的文字的背景颜色就变成黄色的了。


## XCode8版本问题
Xcode8 不再支持`Application Plug-in`插件
[XCode8.2.1继续使用xcode插件](http://www.jianshu.com/p/39443429f71d)
这个是因为苹果解决xcode ghost，把插件屏蔽了。
解决方法
`sudo /usr/libexec/xpccachectl`
然后必须重启电脑后生效.


