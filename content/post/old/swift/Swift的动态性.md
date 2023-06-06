---
title: Swift的动态性
date: 2018-11-10 09:14:55
updated: 2018-11-10 09:14:55
comments: true
tags: [swift]
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

## Foundation

Foundation 框架实现了基于运行时一个特性的两个API：
1. `键值编码 (key-value-coding, KVC) `
2. `键值观察 (key-value observing, KVO)`
KVC 和 KVO 允许我们将 UI 和数据进行绑定，这也是 Rx 以及其他响应式框架实现的基础。
### KVC
KVC 的工作方式如下所示：
```objc
@property (nonatomic, strong) NSNumber *number;

[myClass valueForKey:@"number"];
[myClass setValue:@(4) forKey:@"number"];
```
例如，假设我们有这个 number 属性，您可以将属性名称作为键，来获取属性值或者设置属性值。这个功能可以用在此前我们所看到的获取变量列表、协议列表，以及危险的混淆功能当中。
### KVO
通常所说的 Objective-C 「动态性」，往往都是指 KVO。虽然还有其余的函数，但是这些是最常见、最常用的。这也就是人们所说的，Swift 缺失的部分。
1. 使用KVO对状态的变化进行注册
```objc
[myClass addObserver:self
forKeyPath:@"number"
options:NSKeyValueObservingOptionInitial | NSKeyValueObservingOptionNew
context:nil];

- (void)observeValueForKeyPath:(NSString *)keyPath
ofObject:(id)object
change:(NSDictionary<NSKeyValueChangeKey,id> *)change
context:(void *)context{
// Respond to observation.
}
```
在观察的值发生变更之后，KVO 会调用此方法立即通知观察者。通过这个方法，我们便可以按需更新 UI。
2. 弊端：难以调试
KVO这些操作都存有隐患。比方说 KVO，特别是当我们对某个不是自己所创建的类进行观察时，往往会发现有出乎意料的变化发生。通常而言，这些问题是非常难以调试的，也很难去理解为什么出错。在实际产品当中，我并不建议使用它们，尽管它们非常好用。但是在实际产品当中，我会很谨慎地去使用这些功能。

Apple 也是如此认为的，因此它们在视图控制器当中添加了这个私有方法，可以使用 class-dump 来查看。

+ (void)                   attentionClassDumpUser:
yesItsUsAgain:
althoughSwizzlingAndOverridingPrivateMethodsIsFun:
itWasntMuchFunWhenYourAppStoppedWorking:
pleaseRefrainFromDoingSoInTheFutureOkayThanksBye:

的确，很让人抓狂。

## Swift

Swift 是一种强类型语言。即默认类型是安全的静态类型。如果需要的话，不安全类型也是存在的，但是 Swift 仍然是尽力推动我们使用安全的静态类型。Swift 中的动态性可以通过 Objective-C 运行时来获得。

本来这是很好的，但是 Swift 开源并迁移到 Linux 之后，由于 Linux 上的 Swift 并不提供 Objective-C 运行时，事情就大条了。社区的关键点在于，让 Swift 未来能够自己配备动态性，而不是依赖于 Apple。
### Swift中的两个动态修饰符
1. `@objc`: 将Swift API 暴露给 Objective-C 运行时，但是它仍然不能保证编译器会尝试对其进行优化。
2. `@dynamic`:动态功能修饰符，它隐含添加了 `@objc`功能。

### Swift中运行时方法
回到我们的动态特性当中，让我们来看一看 Swift 当中这些动态特性是什么样的。假设我们需要使用内省机制、转发方法、替换和绑定方法。
#### 方法转发
```objc
// 1
override class func resolveInstanceMethod(_ sel: Selector!)
-> Bool {
// 添加实例方法并返回 true 的一次机会，它随后会再次尝试发送消息
}

// 2
override func forwardingTarget(for aSelector: Selector!) ->
Any? {
// 返回可以处理 Selector 的对象
}

// 3 - Swift 不支持 NSInvocation
```
resolveInstanceMethod 同样会被调用，forwardingTarget 看起来似乎更贴近于 Swift 3 风格的 API。但是 NSInvocation 并不能在 Swift 当中使用。我们同样可以实现方法转发，因此看起来也不算太坏。
#### 方法混淆
`load` 在 Swift 不再会被调用，因此我们需要在 `initialize` 中进行混淆。在 Objective-C 当中使用的 `dispatch_once`，但是在 Swift 3 中被废弃。事情变得略为复杂。虽然对于特定类型的函数而言，我们仍然可以将其定义为动态函数，但是它会消除大部分混淆的功能。

#### 内省机制
```
if self is MyClass {
// YAY
}

let myString = "myString";
let mirror = Mirror(reflecting: myString)
print(mirror.subjectType) // “String"
let string = String(reflecting: type(of:
myString)) // Swift.String

// No native method introspection
```
`is` 替代了 `isMemberOfClass`，它同样也可以对 Swift 值类型:结构体、枚举以及其他 Swift 当中的新类型使用。此外还有一个新的映射 API，它主要针对于管道 (pipe) 和数据。
#### 单元测试
目前，我们没有原生的办法来实现内省。这也预示着这个功能未来可能会出现，但是目前我们还无法实现。这很令人沮丧，特别是当您想到我们此前所实现的 XCTestCase。如果您打算为 Linux 编写单元测试的时候，就无法自动遍历所有的函数。您必须实现 static var allTests，然后手动列出所有的测试函数。这很糟糕。

#### KVC/KVO功能的削弱
KVO 的魅力在于，您可以在不是自己所创建的类当中使用它，也可以只对您想要监听变化的类使用。KVO 和 KVC 在 Swift 被极大地削弱了。
两点要求：
1. 被观察的对象必须要继承自 NSObject，并且使用一个 Objective-C 类型。
2. 被观察的变量必须要声明为 @dynamic。您必须要对想要观察的事务了如指掌。
问题是 Swift 并没有很好的替代方案。您可以使用 Rx 或者基于协议来观察对象。但是语言自身是没有原生的解决方案的。

## 总结
总而言之，Objective-C 的动态性无疑是非常强大的、极其有用，虽然也存在危险性。Swift 目前没有足够的替代方案来解决这些问题，但是可以预见在不久的将来 Swift 的动态性将会出现，这是值得我们期待的。
