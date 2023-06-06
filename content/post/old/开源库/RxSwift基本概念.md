---
title: RxSwift基本概念
date: 2017-03-03 13:01:41
updated: 2018-10-05 21:42:36
comments: true
tags: [SDK]
categories:
- 学习笔记
keywords: 
permalink: 
---
## FRP函数式反应型编程 
Functional Reactive Programming ， 缩写为 FRP

## 函数编程
### 函数是第一等公民
所谓 第一等公民 (first class) ，指的是函数与其他数据类型一样，处于平等地位。既可以赋值给其他变量，也可以作为参数传入另一个函数，或者作为别的函数的返回值。
将数组通过指定的函数映射成另一个数组，例如将`increment函数`作为参数传入数组的`map函数`:
{% codeblock lang:swift %}
let increment = { return $0 + 1 }
[1,2,3].map(increment)  // [2,3,4]
{% endcodeblock %}

### 函数式的函数

#### map 映射函数
`map` 可以把一个数组按照一定的规则转换成另一个数组，定义如下：
{% codeblock lang:swift %}
func map<U>(transform: (T) -> U) -> U[]
//效果
[ x1, x2, ... , xn].map(f) -> [f(x1), f(x2), ... , f(xn)]
{% endcodeblock %}
`map`接受一个把 `T` 类型的转换成 `U` 类型的`transform`函数，最终返回的是 `U 类型的集合`。

#### filter 筛选函数
`filter`通过在闭包中对每个元素进行逻辑运算，来排除为`false`的元素
{% codeblock lang:swift %}
func filter(includeElement: (T) -> Bool) -> [T]
//实现
var oldArray = [10,20,45,32]
var filteredArray  = oldArray.filter({ return $0 > 30 })
println(filteredArray) // [45, 32]
{% endcodeblock %}

#### reduce 迭代函数
`reduce`函数把`U`类型集合中的所有元素，以`initial`为初始值，按照`combine`规则，逐个迭代并返回一个U类型的对象。
定义如下：
{% codeblock lang:swift %}
func reduce<U>(initial: U, combine: (U, T) -> U) -> U
{% endcodeblock %}
reduce 有两个参数，`initial`是初始值，`combine`定义规则闭包，闭包有两个输入的参数，一个是原始值，一个是新进来的值，闭包返回的新值作为下一轮循环中的`initial`值。
写几个小例子试一下：
{% codeblock lang:swift %}
var oldArray = [10,20,45,32]
var sum = 0
sum = oldArray.reduce(0,{$0 + $1}) // 0+10+20+45+32 = 107
sum = oldArray.reduce(1,{$0 + $1}) // 1+10+20+45+32 = 108
sum = oldArray.reduce(5,{$0 * $1}) // 5*10*20*45*32 = 1440000
sum = oldArray.reduce(0,+) // 0+10+20+45+32 = 107
println(sum)
{% endcodeblock %}

### 函数式和指令式的比较

## 响应式编程 - Reactive
在日常开发中，我们经常需要监听某个属性，并且针对该属性的变化做一些处理。比如以下几个场景：
1. 用户在输入邮箱的时候，监测输入的内容并在界面上提示是否符合邮箱规范。
2. 用户在修改用户名之后，所有显示用户名的界面都要改为新的用户名。
外部输入信号的变化、事件的发生，这些都是典型的外部环境变化。根据外部环境的变化进行响应处理，直观上来讲像是一种自然地反应。我们可以将这种自动对变化作出响应的能力称为反应能力 (Reactive) 。

那么什么是反应型编程呢？

    Reactive programming is programming with asynchronous data streams.
    反应型编程是异步数据流的编程。

对于移动端来说，异步数据流的概念并不陌生，变量、点击事件、属性、缓存，这些就可以成为数据流。
我们可以通过一些简单的 ASCII 字符来演示如何将事件转换成数据流：
    --a---b-c---d---X---|-->
    a, b, c, d 是具体的值，代表了某个事件
    X 表示发生了一个错误
    | 是这个流已经结束了的标记
    ----------> 是时间轴
比如我们要统计用户点击鼠标的次数，那么可以这样：
clickStream:    ---c----c--c----c------c-->
                vvvvv map(c becomes 1) vvvv
                ---1----1--1----1------1-->
                vvvvvvvvv scan(+) vvvvvvvvv
counterStream:  ---1----2--3----4------5-->
反应型编程就是基于这些数据流的编程。而函数式编程则相当于提供了一个工具箱，可以方便的对数据流进行合并、创建和过滤等操作。

## swift 函数式编程
Swift 是苹果公司在 2014 年推出的编程语言，用于编写 iOS 和 OS X 应用程序。它吸收了很多其它语言的语法特性，例如闭包、元组、泛型、结构体等等，这使得它的语法简洁而灵活。
Swift 本身并不是一门函数式语言，不过有一些函数式的方法和特性
1. map reduce 等函数式函数
2. 函数是一等公民
3. 模式匹配
我们并不能因为 Swift 中的一些函数式特性就把它归为函数式语言，但是我们可以利用这些特性进行函数式 Style 的编程。

# RxSwift 响应式编程
[Rx.playground](https://github.com/ReactiveX/RxSwift/tree/master/Rx.playground)

## Observable观察者模式
Rx 的基础：`Observable` ， `Observable<Element>` 是观察者模式中可观察的对象，相当于一个事件序列 (GeneratorType)。
支持订阅的事件序列，在下文简称为`订阅源`或`可观察者`。
订阅源的事件队列中包括三种事件类型：
1. `.Next(value)`: 表示新的事件数据。
2. `.Completed`: 表示事件序列的完结。
3. `.Error`: 同样表示完结，但是代表异常导致的完结。

### 新建订阅源几种快捷方法
1. `empty`是一个空的序列，它只发送 `.Completed` 消息。
{% codeblock Observable+Creation.swift lang:swift %}
public static func empty() -> Observable<E>
example("empty") {
    let emptySequence: Observable<Int> = empty()
    let subscription = emptySequence.subscribe { event in
                                                print(event)
                                            }
}
--- empty example ---
Completed
{% endcodeblock %}
2. `never` 是没有任何元素、也不会发送任何事件的空序列。
{% codeblock Observable+Creation.swift lang:swift %}
/**
- returns: An observable sequence whose observers will never get called.
*/
public static func never() -> Observable<E>
{% endcodeblock %}
3. `just` 是只包含一个元素的序列，它会先发送 `.Next(value)` ，然后发送 `.Completed`
{% codeblock Observable+Creation.swift lang:swift %}
/**
Returns an observable sequence that contains a single element.
*/
public static func just(_ element: E) -> Observable<E> {
return Just(element: element)
}
{% endcodeblock %}
4. `sequenceOf` 可以把一系列元素转换成订阅源
{% codeblock lang:swift %}
let sequenceOfElements/* : Observable<Int> */ = sequenceOf(0, 1, 2, 3)
{% endcodeblock %}
5. `asObservable方法` 将遵守`ObservableType`协议的对象转为可观察者序列
{% codeblock ObservableType.swift lang:swift %}
public protocol ObservableType : ObservableConvertibleType
{
    //Default implementation of converting `ObservableType` to `Observable`.
    public func asObservable() -> Observable<E>
}
let sequenceFromArray = [1, 2, 3, 4, 5].asObservable()
{% endcodeblock %}
6. `failWith`创建一个没有元素的序列，只会发送失败 (`.Error`) 事件。
{% codeblock lang:swift %}
let error = NSError(domain: "Test", code: -1, userInfo: nil)
let erroredSequence: Observable<Int> = failWith(error)
let subscription = erroredSequence.subscribe { event in print(event)}
--- failWith example ---
Error(Error Domain=Test Code=-1 "The operation couldn’t be completed. (Test error -1.)")
{% endcodeblock %}

### create自定义订阅源
`create` 可以通过闭包创建序列，通过 `.on(e: Event)` 添加可观察者事件。
{% codeblock lang:swift %}
example("create")
{
    let myJust = { (singleElement: Int) -> Observable<Int> in
        return create { observer in
                observer.on(.Next(singleElement))
                observer.on(.Completed)
                return NopDisposable.instance
        }
    }

    let subscription = myJust(5).subscribe { event in
            print(event)
    }
}
--- create example ---
Next(5)
Completed
{% endcodeblock %}

### deferred订阅源的懒加载
`deferred`表示当有有新增订阅者第一次订阅了该订阅源时，订阅源才会被创建，且每个订阅者订阅的对象都是内容相同而完全独立的序列。
{% codeblock lang:swift %}
example("TestDeferred") 
{
    var value: String? = nil
    var subscription: Observable<String?> = deferred {
        return just(value)
    }
    // got value
    value = "Hello!"
    subscription.subscribe { event in
        print(event)
    }
}
--- TestDeferred example ---
Next(Optional("Hello!"))
Completed
{% endcodeblock %}

## 几种特殊类型的订阅源
`Subject` 可以看做是一种代理和桥梁。它既是订阅者又是订阅源，这意味着它既可以订阅其他 `Observable 对象`，同时又可以对它的订阅者们发送事件。

### PublishSubject 向所有订阅者发送事件队列
当`PublishSubject`类型订阅源事件队列中`.on()`新增事件时，会触发所有订阅者，一起响应该事件。
{% codeblock lang:swift %}
example("PublishSubject") {
    let subject = PublishSubject<String>()
    writeSequenceToConsole("1", sequence: subject)
    subject.on(.Next("a"))
    subject.on(.Next("b"))
    writeSequenceToConsole("2", sequence: subject)
    subject.on(.Next("c"))
    subject.on(.Next("d"))
}
--- PublishSubject example ---
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Next(d)
Subscription: 2, event: Next(d)
{% endcodeblock %}

### 基于PublishSubject的几种补发式订阅源
以下几种类型的订阅源，相较第一种仅多了补发历史事件，姑且称为`补发式订阅源`。

#### ReplaySubject先向最新订阅者补发所有已发生的事件
当`ReplaySubject`类型的订阅源，新增订阅者时，该类型的订阅源会把之前已发送过的所有事件队列重新补发给这个最新订阅者。这样就迫使订阅者会对从历史的事件队列逐一响应。
`bufferSize` 是缓冲区的大小，决定了补发队列的最大值。如果 `bufferSize` 是1，那么新的订阅者出现的时候就会补发上一个事件，如果是2，则补两个，以此类推。
{% codeblock lang:swift %}
example("ReplaySubject") 
{
    let subject = ReplaySubject<String>.create(bufferSize: 1)
    writeSequenceToConsole("1", sequence: subject)
    subject.on(.Next("a"))
    subject.on(.Next("b"))
    writeSequenceToConsole("2", sequence: subject)
    subject.on(.Next("c"))
    subject.on(.Next("d"))
}
--- ReplaySubject example ---
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 2, event: Next(b) // 补了一个 b
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Next(d)
Subscription: 2, event: Next(d)
{% endcodeblock %}

#### BehaviorSubject 先向最新订阅者补发最近一次历史事件
`BehaviorSubject`类型的订阅源会向最新订阅者发送最近一次的历史事件队列，如果没有则发送一个默认值。
{% codeblock lang:swift %}
example("BehaviorSubject") 
{
    let subject = BehaviorSubject(value: "z")
    writeSequenceToConsole("1", sequence: subject)
    subject.on(.Next("a"))
    subject.on(.Next("b"))
    writeSequenceToConsole("2", sequence: subject)
    subject.on(.Next("c"))
    subject.on(.Completed)
}
--- BehaviorSubject example ---
Subscription: 1, event: Next(z)
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 2, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Completed
Subscription: 2, event: Completed

{% endcodeblock %}

#### Variable 
`Variable` 是基于 `BehaviorSubject` 的一层封装，它的优势是：不会被显式终结。
即：不会收到 `.Completed` 和 `.Error` 这类的终结事件，它会主动在析构的时候发送 `.Complete`
{% codeblock lang:swift %}
example("Variable")
{
    let variable = Variable("z")
    writeSequenceToConsole("1", sequence: variable)
    variable.value = "a"
    variable.value = "b"
    writeSequenceToConsole("2", sequence: variable)
    variable.value = "c"
}
--- Variable example ---
Subscription: 1, event: Next(z)
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 2, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Completed
Subscription: 2, event: Completed
{% endcodeblock %}

## 支持订阅的嵌套函数

### map{规则} 遍历映射
通过闭包中定义的规则将队列中的数据映射到新的队列中，支持订阅遍历事件。
{% codeblock lang:swift %}
func map<U>(transform: (T) -> U) -> U[]
//效果
[ x1, x2, ... , xn].map(f) -> [f(x1), f(x2), ... , f(xn)]
{% endcodeblock %}
`map`接受一个把 `T` 类型的转换成 `U` 类型的`transform`函数，最终返回的是 `U 类型的集合`。
{% codeblock lang:swift %}
example("map") 
{
    let originalSequence = sequenceOf(1,2,3)
    originalSequence.map{ $0 * 2 }.subscribe{ print($0) }
}
--- map example ---
Next(2)
Next(4)
Next(6)
Completed
{% endcodeblock %}

### flatMap 嵌套式遍历映射
嵌套式遍历描述：在遍历当前队列的过程中，每次执行闭包时都会遍历另一个嵌套队列中的所有事件。
可以想象嵌套for 循环来理解。
{% codeblock lang:swift %}
example("flatMap") 
{
    let sequenceInt = sequenceOf(1, 2, 3)
    let sequenceString = sequenceOf("A", "B", "--")
    sequenceInt.flatMap{ int in sequenceString }.subscribe{ print($0) }
}
--- flatMap example ---
Next(A)
Next(B)
Next(--)
Next(A)
Next(B)
Next(--)
Next(A)
Next(B)
Next(--)
Completed
{% endcodeblock %}

### scan(initial:U,combine:(U, T) -> U)迭代映射
scan 有点像 reduce ，把`U`类型集合中的所有元素，以`initial`为初始值，按照`combine`规则，逐个迭代并返回一个U类型的对象。
{% codeblock lang:swift %}
example("scan")
{
    let sequenceToSum = sequenceOf(0, 1, 2, 3, 4, 5)
    sequenceToSum.scan(0) { acum, elem in acum + elem }
    .subscribe {
        print($0)
    }
}
--- scan example ---
Next(0)
Next(1)
Next(3)
Next(6)
Next(10)
Next(15)
Completed
{% endcodeblock %}

## Filtering 支持订阅的过滤器

### filter{布尔语句} 条件过滤法
{% codeblock lang:swift %}
example("filter")
{
let subscription = sequenceOf(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
.filter { $0 % 2 == 0 }.subscribe { print($0) }
}
--- filter example ---
Next(0)
Next(2)
Next(4)
Next(6)
Next(8)
Completed
{% endcodeblock %}

### distinctUntilChanged() 去重过滤法（相邻且不重复）
{% codeblock lang:swift %}
example("distinctUntilChanged") 
{
    let subscription = sequenceOf(1, 2, 3, 1, 1, 4)
    .distinctUntilChanged().subscribe{ print($0) }
}
--- distinctUntilChanged example ---
Next(1)
Next(2)
Next(3)
Next(1)
Next(4)
Completed
{% endcodeblock %}

### take(int) 掐尖过滤法
`take`只获取队列中前 n 个事件，在满足数量之后会自动 `.Completed` 
{% codeblock lang:swift %}
example("take") 
{
let subscription = sequenceOf(1, 2, 3, 4, 5, 6)
.take(3).subscribe { print($0) }
}
--- take example ---
Next(1)
Next(2)
Next(3)
Completed
{% endcodeblock %}

## Combining 订阅源聚合器
订阅源聚合器将多个可观察者（订阅源）合并成一个可观察者（聚合订阅源），这样更便于订阅者同时监听多个订阅源。

### startWith 向可观察者队列中添加排头兵（新增的可观察者）
{% codeblock lang:swift %}
let subscription = sequenceOf(4, 5, 6).startWith(3).subscribe { print($0)}
--- startWith example ---
Next(3)
Next(4)
Next(5)
Next(6)
Completed
{% endcodeblock %}

### combineLatest 合并聚合订阅源最后一次事件数据，生成一个聚合事件
便于订阅者监听聚合订阅源中每个订阅源的最后一次事件数据
{% codeblock lang:swift %}
example("combineLatest 1") 
{
    let intOb1 = PublishSubject<String>()
    let intOb2 = PublishSubject<Int>()
    combineLatest(intOb1, intOb2) {"\($0) \($1)"}.subscribe {  print($0) }
    intOb1.on(.Next("A"))
    intOb2.on(.Next(1))
    intOb1.on(.Next("B"))
    intOb2.on(.Next(2))
}
--- combineLatest 1 example ---
Next(A 1)
Next(B 1)
Next(B 2)
{% endcodeblock %}

### zip(intOb1, intOb2) 拉链式合并
仅在凑齐聚合源中所有订阅源的事件时，才会聚合一次，触发订阅者的响应。可以将多达8个订阅源
{% codeblock lang:swift %}
example("zip 1") 
{
    let intOb1 = PublishSubject<String>()
    let intOb2 = PublishSubject<Int>()
    zip(intOb1, intOb2) { "\($0) \($1)" }.subscribe { print($0)}
    intOb1.on(.Next("A"))
    intOb2.on(.Next(1))
    intOb1.on(.Next("B"))
    intOb1.on(.Next("C"))
    intOb2.on(.Next(2))
}
--- zip 1 example ---
Next(A 1)
Next(B 2)
{% endcodeblock %}


### merge() 按可观察者的新的事件次序合并队列
订阅者会按次序来响应聚合订阅源的每一件事件
{% codeblock lang:swift %}
example("merge 1") 
{
    let subject1 = PublishSubject<Int>()
    let subject2 = PublishSubject<Int>()
    sequenceOf(subject1, subject2).merge().subscribeNext { int in print(int)}
    subject1.on(.Next(1))
    subject1.on(.Next(2))
    subject2.on(.Next(3))
    subject1.on(.Next(4))
    subject2.on(.Next(5))
}
--- merge 1 example ---
1
2
3
4
5
{% endcodeblock %}

### switchLatest订阅源切换器：用于嵌套式订阅源
通过切换（var3.value）嵌套的订阅源，来切换订阅者当前监听的订阅源，以响应当前订阅源中的可观察者的事件。
{% codeblock  lang:swift %}
example("switchLatest") 
{
    let var1 = Variable(0)
    let var2 = Variable(200)
    // var3 is like an Observable<Observable<Int>>
    let var3 = Variable(var1)
    let d = var3.switchLatest().subscribe{ print($0) }
    var1.value = 1
    var1.value = 2
    var1.value = 3
    var1.value = 4
    var3.value = var2
    var2.value = 201
    var1.value = 5
    var3.value = var1
    var2.value = 202
    var1.value = 6
}
--- switchLatest example ---
Next(0)
Next(1)
Next(2)
Next(3)
Next(4)
Next(200)
Next(201)
Next(5)
Next(6)
{% endcodeblock %}

## 订阅器
1. 订阅器：`subscribe` 用来订阅可观察者的事件队列（即订阅源），并指定响应的相关操作
2. Next订阅器：`subscribeNext` 只订阅 `.Next` 事件
3. Completed订阅器：`subscribeCompleted` 只订阅`.Completed` 完成事件
4. Error订阅器：`subscribeError` 订阅 `.Error` 失败事件
5. 订阅器观察者：`doOn` 在订阅器执行之前，获得执行代码的机会。可对比理解：`swift中的属性观察者`

## 控制take掐尖过滤器
过滤器通过指定的事件和状态语句来判断是否停止take

### takeUntil 得到指定事件之后触发 .Completed 事件
{% codeblock lang:swift  %}
example("takeUntil") 
{
    let originalSequence = PublishSubject<Int>()
    let whenThisSendsNextWorldStops = PublishSubject<Int>()
    originalSequence.takeUntil(whenThisSendsNextWorldStops)
    .subscribe {
        print($0)
    }
    originalSequence.on(.Next(1))
    originalSequence.on(.Next(2))
    whenThisSendsNextWorldStops.on(.Next(1))
    originalSequence.on(.Next(3))
}
--- takeUntil example ---
Next(1)
Next(2)
Completed
{% endcodeblock %}

### takeWhile 判断语句
takeWhile 则是可以通过状态语句判断是否继续 take 。
{% codeblock lang:swift %}
example("takeWhile") 
{
    let sequence = PublishSubject<Int>()
    sequence.takeWhile { int in int < 2 }.subscribe { print($0) }
    sequence.on(.Next(1))
    sequence.on(.Next(2))
    sequence.on(.Next(3))
}
--- takeWhile example ---
Next(1)
Completed
{% endcodeblock %}


























