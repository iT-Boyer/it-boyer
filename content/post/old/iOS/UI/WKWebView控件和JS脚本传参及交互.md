---
title: WKWebView控件和JS脚本传参及交互
date: 2017-07-07 09:54:41
updated: 2018-07-03 12:30:31
comments: true
tags: [JS]
categories:
- 学习笔记
keywords: 
permalink: 
---
### WebKit简介
{% github it-boyer WKWebView-JS d03bc0e width = 30% %}
自iOS8 以后，苹果推出了新框架 WebKit，提供了替换 UIWebView 的组件 WKWebView。各种 UIWebView 的性能问题没有了，速度更快了，占用内存少了，体验更好了，下面列举一些其它的优势:
1、在性能、稳定性、功能方面有很大提升（加载速度，内存的提升谁用谁知道）
2、更多的支持 HTML5 的特性
3、官方宣称的高达60fps的滚动刷新率以及内置手势
4、Safari 相同的 JavaScript 引擎
5、将 UIWebViewDelegate 与 UIWebView 拆分成了14类与3个协议，包含该更细节功能的实现。



```puml
title WKWebView和JS脚本
center header
了解传参原理和交互机制
endheader

'******* 类声明模块 *******'
'类型:class,abstract,interface,annotation,enum'
'访问域:(-)私有,(#)保护,(~)包私有,(+)公有'

'&&&&&& 类组合模块 类模块 &&&&&&&'
'六种组合样式:Node,Rectangle,Folder,Frame,Cloud,Database'
package "iOS原生API"<<Folder>>{
    class WK<WKWebView>{
        __ 方法组 __
        + (id)initWithFrame:configuration:
        + (void)evaluateJavaScript:completionHandler:
    }

    class config<WKWebViewConfiguration>{
        -- 属性组 --
        + WKUserContentController *userContentController
        __方法组__
        +(id)init
    }

    class userCC<WKUserContentController>{
        __方法组__
        +(void)addScriptMessageHandler:name:
        +(void)addUserScript:
    }
    
    class delegate<WKScriptMessageHandler>{
    --代理方法--
    +(void)userContentController:didReceiveScriptMessage:
    }
    
    class script<WKUserScript>{
    --构造器--
     + (id)initWithSource:injectionTime:forMainFrameOnly:;
    }
}

package JS脚本 <<Folder>> #red{
    class js脚本{
        --配置组--
        messageHandlers:js接口名
        postMessage参数
    }
}

'###### 类备注模块 类声明末尾使用:note 位置: 备注#########'
note top of WK
浏览器主体
end note
note right of config #gray
用于在构造WK时，预加载js脚本，设置监听JS接口清单
end note
note bottom of userCC:1.提供预加载JS脚本操作\n2.在OC中添加监听的接口清单：JS脚本的接口名\n3.注入代理类
note top of js脚本: 调用原生\n window.webkit.messageHandlers.js接口名.postMessage(参数)\n 参数为id类型，当无参调用时，参数设置为null
note right of delegate:代理方法：拦截监听JS接口，获取JS传递的参数值\n在回调中获取该参数值：message.body

'>>>>>> 类关系图及连接备注模块 >>>>>>>>'
'关系节点符:(|>)继承,(*)合成 ,(o)聚合, 其他#,x,},+,^ 连线符:(--)实线 ，(..)虚线'
WK"1" *-[#red]- "1" config:构造参数 <
note right on link #white
构造WK时，配置webView的预设条件
end note

config *-[#green]- userCC:核心组件 <

script o-left[#red]- js脚本:浏览器预加载JS脚本 <
WK o-left[#red]- js脚本:浏览器调用JS脚本 <
userCC *-[#blue] delegate:处理监听到的JS脚本 <
delegate <|.left[#black]. WK: 代理类 <
userCC o-left[#gray]- script:注入脚本 <

center footer
boyer模型
endfooter
```

#### 添加监听代理和JS接口
在OC中添加监听的接口清单：以JS脚本的接口`showMobile`为例：
```objc
WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
WKUserContentController *userCC = config.userContentController;
//MARK:在OC中添加监听的接口清单：JS脚本的接口名
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
#### 设置WKUserContentController的代理

1. 设置代理类遵守`WKScriptMessageHandler`协议
```objc
@interface ViewController () <WKScriptMessageHandler>
```
2. 注册对JS接口监听，注入代理类
```objc
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
3. 实现`WKUserContentController`代理的回调方法,响应JS接口事件
```objc
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
NSLog(@"%@",message.body);
}
```

#### JS脚本接口
js接口声明格式：
```js
window.webkit.messageHandlers.接口名.postMessage('参数')
```
接口名: 在WKWebView中，当JS执行该接口时，OC会拦截预先监听的接口，并处理相关事件。

参数：object类型，多个参数时需要封装为集合类型来实现多参传递。

当OC拦截到该接口时，可以在`WKScriptMessageHandler`回调方法中的`WKScriptMessage`参数实例中获取该参数值: `message.body`。

三个例子：
1. JS无参调用OC<br/>
当无参调用OC时，参数必须为`null`
```js
window.webkit.messageHandlers.showMobile.postMessage(null)
```

2. JS传参调用OC<br/>
传递单个参数时，直接写入即可，例如：`xiao黄`
```js
window.webkit.messageHandlers.showName.postMessage('xiao黄')
```
传递多个参数时，需要封装为集合类型实现多参传递。<br/>
例如:当传递一个电话，一条信息，需要封装为`['13300001111','Go Climbing This Weekend !!!']`
```js
window.webkit.messageHandlers.showSendMsg.postMessage(['13300001111', 'Go Climbing This Weekend !!!'])
```
### iOS原生API调用JS脚本
在网页加载完成之后调用JS代码才会执行，因为这个时候html页面已经注入到webView中并且可以响应到对应方法。<br/>
例如调用JS函数`alertMobile()`：
```objc
[self.wkWebView evaluateJavaScript:@"alertMobile()" completionHandler:^(id _Nullable response, NSError * _Nullable error) {
//TODO
NSLog(@"%@ %@",response,error);
}];
```

#### 在OC中为JS定义属性/函数

* 当注入的类型字符串类型时，必须用`''`括起来。<br/>
* OC注入的参数为全局属性，在html中的JS脚本可以直接调用属性名来获取值。<br/>

通过NSString形式，编写JS脚本，通过以下两种方式注入网页

方式一：在初始化`WKWebView`时，通过配置`WKWebViewConfiguration>userContentController`注入JS脚本  。
```objc
//MARK:向网页中注入JS脚本例如，参数/函数等
WKUserScript *script = [[WKUserScript alloc] initWithSource:@"var number=0;"
injectionTime:WKUserScriptInjectionTimeAtDocumentStart
forMainFrameOnly:YES];
WKUserContentController *userCC = config.userContentController;
[userCC addUserScript:script];
```
方式二：使用WKWebView实例方法`evaluateJavaScript`动态注入JS脚本

```objc
[self.wkWebView evaluateJavaScript:@"var number=0;" completionHandler:nil];
```
#### iOS原生API调用JS函数
使用WKWebView实例方法`evaluateJavaScript`动态调用JS函数
```objc
[self.wkWebView evaluateJavaScript:@"alertSendMsg('18870707070','下午好！')" completionHandler:nil];
```
