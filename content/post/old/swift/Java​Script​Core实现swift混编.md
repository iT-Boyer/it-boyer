---
title: Java​Script​Core实现swift混编
date: 2017-02-12 13:34:06
updated: 2019-01-16 21:01:14
comments: true
tags: [JS,macOS]
categories:
- 解决方案
keywords: 混编,IOS
permalink: 
---
OS X Mavericks 和 iOS 7 引入了 `JavaScriptCore` 库，它把 `WebKit 的 JavaScript` 引擎用 `Objective-C` 封装，提供了简单，快速以及安全的方式接入世界上最流行的语言。不管你爱它还是恨它，`JavaScript` 的普遍存在使得程序员、工具以及融合到 OS X 和 iOS 里这样超快的虚拟机中资源的使用都大幅增长。

1. 如何从 `JavaScript 环境`中提取值以及如何调用其中定义的函数?
2. 那么反向呢？怎样从 `JavaScript` 访问我们在 `Objective-C `或 `Swift` 定义的对象和方法？

## swift通过JSContext调用JavaScript
如何从`JavaScript 环境`中提取值以及如何调用其中定义的函数?

### 全局实例JSContext：运行JavaScript代码的环境
全局实例`JSContext`作用就像在浏览器内运行的一个`JavaScript`脚本，`JSContext` 类似于 `window`。
通过`JSContext`实例运行`JavaScript`代码`创建变量`，`做计算`，`定义方法`：
{% codeblock  lang:swift %}
let context = JSContext()    //创建全局环境
context.evaluateScript("var num = 5 + 5") //计算
context.evaluateScript("var names = ['Grace', 'Ada', 'Margaret']") //定义变量
context.evaluateScript("var triple = function(value) { return value * 3 }") //定义方法
let tripleNum: JSValue = context.evaluateScript("triple(num)")  
{% endcodeblock %}

### 动态类型JSValue:包裹JSContext环境下每一个可能的JS值
`JavaScript` 是动态语言，所以动态类型`JSValue`包裹JSContext环境中任何可能的JS值，字符串和数字；数组、对象和方法；甚至错误和特殊的 JavaScript 值诸如 `null` 和 `undefined`。
获取`tripleNum`值：
{% codeblock  lang:swift %}
println("Tripled: \(tripleNum.toInt32())")
{% endcodeblock %}
`JSValue` 包括一系列方法用于访问其可能的值以保证有正确的 `Foundation 基本类型`
包括：

### 下标取值：访问JSContext环境下的任何值
`JSContext` 和 `JSValue` 实例可以使用下标的方式访问之前创建的 `context` 的任何值。
* `JSContext`：需要一个字符串下标
* `JSValue`：允许使用`字符串`或`整数`标来得到里面的对象和数组

#### JSContext下标取值
1. `swift`语法
{% codeblock 未映射成[]的原始方法 lang:swift http://nshipster.cn/object-subscripting/ objectAtKeyedSubscript()和objectAtIndexedSubscript() %}
let names = context.objectForKeyedSubscript("names")  //JSContext字符串下标原始方法
{% endcodeblock %}

#### JSValue整数下标原始方法
{% codeblock 未映射成[]的原始方法 lang:swift http://nshipster.cn/object-subscripting/ objectAtKeyedSubscript()和objectAtIndexedSubscript() %}
let initialName = names.objectAtIndexedSubscript(0)   //JSValue整数下标原始方法
println("The first name: \(initialName.toString())")  //JSValue method
// The first name: Grace
{% endcodeblock %}

>在这里，Objective-C 代码可以利用下标表示法，如下例：context[@"names"]，names[0]，[initialName toString]，Swift 目前只公开[原始方法](http://nshipster.cn/object-subscripting/):`objectAtKeyedSubscript()` 和 `objectAtIndexedSubscript()`来让下标成为可能。

### callWithArguments调用JS方法：只需传入Foundation基本类型参数
上述`JavaScript`代码中，`JSValue`包装了一个`triple函数`，在`Objective-C / Swift` 代码中可以使用 `Foundation基本类型`作为参数来直接调用该函数。再次，`JavaScriptCore` 很轻松的处理了这个桥接：
{% codeblock lang:swift %}  
let tripleFunction = context.objectForKeyedSubscript("triple") //下标取值
let result = tripleFunction.callWithArguments([5]) //传入基本类型参数直接调用
println("Five tripled: \(result.toInt32())")
{% endcodeblock %}

### `exceptionHandler`错误处理
`exceptionHandler` 是一个接收`JSContext 引用`和`异常本身`的回调处理的闭包。
通过设置上下文的 `exceptionHandler` 属性，可以观察和记录`语法`，`类型`以及`运行时错误`:
{% codeblock lang:swift %}
context.exceptionHandler = { context, exception in
println("JS Error: \(exception)")
}

context.evaluateScript("function multiply(value1, value2) { return value1 * value2 ")
// JS Error: SyntaxError: Unexpected end of script
{% endcodeblock %}
{% codeblock  lang:objc %}
context.exceptionHandler = ^(JSContext *context, JSValue *exception) {
NSLog(@"JS Error: %@", exception);
};

[context evaluateScript:@"function multiply(value1, value2) { return value1 * value2 "];
// JS Error: SyntaxError: Unexpected end of script
{% endcodeblock %}

## JavaScript 通过JSContext调用 swift／OC
怎样从 `JavaScript` 访问我们在 `Objective-C `或 `Swift` 定义的对象和方法？
让 `JSContext` 访问我们的本地客户端代码的方式主要有两种：
1. `block块`键值对：把OC中的`block块`赋值给`JSContext`的一个标示键，该标识键的`JSValue`可以通过`callWithArguments`调用.
2. `JSExport 协议`。

### block块 键值对:该block键的`JSValue`通过`callWithArguments`调用
当一个 `Objective-C block` 被赋给 `JSContext` 里的一个标识符，`JavaScriptCore` 会自动的把 `block` 封装在 `JavaScript 函数`里，并以该标示符作为函数名来调用该block的实现。这使得在 `JavaScript` 中可以简单的使用 `Foundation` 和 `Cocoa`类，所有的桥接都为你做好了。
[CFStringTransform](http://nshipster.cn/cfstringtransform/)处理语言的强大威力

在 `JSContext` 中使用 `Swift 闭包`需要注意两点:
1. 与 `@objc_block` 属性一起声明
2. 使用Swift中的`unsafeBitCast()`函数，把对象转换为`AnyObject`

{% codeblock lang:swift %}
let simplifyString: @objc_block String -> String = { input in
    var mutableString = NSMutableString(string: input) as CFMutableStringRef
    CFStringTransform(mutableString, nil, kCFStringTransformToLatin, Boolean(0))
    CFStringTransform(mutableString, nil, kCFStringTransformStripCombiningMarks, Boolean(0))
    return mutableString
}
context.setObject(unsafeBitCast(simplifyString, AnyObject.self), forKeyedSubscript: "simplifyString")
//通过simplifyString标示符来调用block的实现
println(context.evaluateScript("simplifyString('안녕하새요!')"))
// annyeonghasaeyo!
{% endcodeblock %}

{% codeblock lang:objc %}
//给标示符赋值一个oc-block，该标示符会被自动装换为JavaScript函数
context[@"simplifyString"] = ^(NSString *input) {
NSMutableString *mutableString = [input mutableCopy];
CFStringTransform((__bridge CFMutableStringRef)mutableString, NULL, kCFStringTransformToLatin, NO);
CFStringTransform((__bridge CFMutableStringRef)mutableString, NULL, kCFStringTransformStripCombiningMarks, NO);
return mutableString;
};

//通过simplifyString标示符来调用block的实现
NSLog(@"%@", [context evaluateScript:@"simplifyString('안녕하새요!')"]);
{% endcodeblock %}
#### 内存管理
由于 `block` 可以保有变量引用，而且 `JSContext` 也强引用它所有的变量，为了避免强引用循环需要特别小心。
避免保有`JSContext` 或`一个 block` 里的任何 `JSValue`。相反，使用 `[JSContext currentContext]` 得到当前上下文，并把你需要的任何值用参数传递。

### JSExport 协议
在继承`JSExport 协议`的子协议里声明的属性，实例方法还是类方法，都会自动暴漏给`JavaScript`代码来调用。


