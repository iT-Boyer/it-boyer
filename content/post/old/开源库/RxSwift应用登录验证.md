---
title: RxSwift应用登录验证
date: 2018-10-04 09:04:35
updated: 2018-10-06 14:50:13
comments: true
tags: [SDK]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github it-boyer SimpleValidation fb7a58b width = 30% %}

<!--音乐欣赏--> 
{% meting "0036mZ7Q1pk7st" "tencent" "song" %}
## 使用RxSwift的优点 
RxSwift的目的是让让数据/事件流和异步任务能够更方便的序列化处理，能够使用swift进行响应式编程

这是一个模拟用户登录的程序。
## 功能点
1. 当用户输入用户名时，如果用户名不足 5 个字就给出红色提示语，并且无法输入密码，当用户名符合要求时才可以输入密码。
2. 同样的当用户输入的密码不到 5 个字时也给出红色提示语。
3. 当用户名和密码有一个不符合要求时底部的绿色按钮不可点击，只有当用户名和密码同时有效时按钮才可点击。
4. 当点击绿色按钮后弹出一个提示框，这个提示框只是用来做演示而已。
###  `share(replay: 1)`是用来做什么的？
我们用 `usernameValid` 来控制用户名提示语是否隐藏以及密码输入框是否可用。[shareReplay](https://beeth0ven.github.io/RxSwift-Chinese-Documentation/content/rxswift_core/operator/shareReplay.md) 就是让他们共享这一个源，而不是为他们单独创建新的源。这样可以减少不必要的开支。

第一个观察者`passwordOutlet.rx_enabled`订阅`usernameValid`时，调用`map`里的`print`函数，第二个观察者在订阅时(没有添加`.shareReplay(1)`)时，又再次调用`map`里的`print`函数，以此类推，如果有很多观察者的话就要调用很多次，而从第二个观察者开始需要的只是`map`返回的一个序列，而不是让其徒劳地调用`map`里的函数，那么怎样解决在多个观察者订阅时多次重复调用执行的问题？ 
使用`shareReplay(bufferSize: Int)`就ok了。 
`shareReplay`会返回一个新的事件序列，它监听底层序列(这里指的是map返回的序列)的事件，并且通知自己的订阅者们。不过和传统的订阅不同的是，它是通过『重播』的方式通知自己的订阅者，因此在这里通过`shareReplay`订阅的`map`并不会调用多次。 
// 参数bufferSize指的是重播的最大元素个数，因为usernameValid是一个只有一个元素的序列observable，因此shareReplay参数为1；假如对于一个有5个元素的序列，你只需要重复播报最后3个，那么就写成.shareReplay(3)，就酱紫。

### `disposed(by: disposeBag)` 是用来做什么的？
和我们所熟悉的对象一样，每一个绑定也是有生命周期的。并且这个绑定是可以被清除的。`disposed(by: disposeBag)`就是将绑定的生命周期交给 `disposeBag` 来管理。当 `disposeBag` 被释放的时候，那么里面尚未清除的绑定也就被清除了。这就相当于是在用 `ARC` 来管理绑定的生命周期。 这个内容会在 [Disposable](https://beeth0ven.github.io/RxSwift-Chinese-Documentation/content/rxswift_core/disposable.html) 章节详细介绍。

### bindTO 简单使用
我的理解就是将已经信号源去用一个`UIBindingObserver`去接受,当接收到的属性是`true`进行相应的操作
为登录按钮写一个rx_click的拓展
```
///首先写一个UIBindingObserver
var rx_click:AnyObserver<Bool> {
return UIBindingObserver.init(UIElement: self, binding: { (lab, result) in
lab.backgroundColor = result ? UIColor.blue:UIColor.gray
}).asObserver()

}

//*bindto 信号合并处理  bindto发送信号
//              combineLatest(合并信号)                       bind(订阅)
//   observer ----------------------->处理信号时间------------->处理信号
///然后手机号11位 验证码 6位就可以用这一行代码解决
let _ = Observable.combineLatest(phoneobser, passobserver, resultSelector: {
$0 && $1
}).bind(to: canclik.rx_click)
```
