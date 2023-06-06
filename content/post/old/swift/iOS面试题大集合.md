---
title: iOS面试题大集合[转]
date: 2017-01-22 18:55:21
updated: 2017-01-22 18:55:21
comments: true
tags: [iOS,资源]
categories:
- 学习笔记
keywords: iOS,面试,z资源
permalink: 
---

<h1 align="center">iOS有用的面试题大集合</h1>

## 面试题从何处得来

* [招聘一个靠谱的 iOS](http://blog.sunnyxx.com/2015/07/04/ios-interview/)
* [知乎－如何面试 iOS 工程师？](http://www.zhihu.com/question/19604641)

## 阅读面试题之前

在正式开始之前，我期望你能对iOS/Mac OS X平台开发有所了解，在iOS开发中已经很少需要自己写复杂的算法了，一般情况下很少会在面试中出现算法的考核，如果你了解一些基础的算法，还是有帮助的。

Now！！请使用ARC

### 什么是iOS开发

iOS是iPhone iPad等手持设备的操作系统，所谓的iOS开发就是开发运行在iOS系统上的应用或者游戏，比如支付宝，微信，微博等，当然这也包括了iPad版的应用，iOS开发可以归纳到**移动开发**领域。

**有时候面试官是那种'脑残粉'，了解一下Apple的发展历史，可能比较聊的开。**

[苹果Mac计算机31年发展历程回顾](http://digi.tech.qq.com/a/20150127/021898.htm)

[苹果公司](http://baike.baidu.com/link?url=68F4Bl4llkNvdFJ1Md0fkZDDudN-NS46JeZoLrgPeqEbZmm8oBKG92Ocyd983yNQU6FVuDTFZOnjjPjfUHnuoePdfh6zJJ973pXFKYcbIKp5bCnQy_WvUVNJ6P84s8HE1xAlRaGdLVuoCb2p_8uaMa)

[苹果公司在知乎上的话题](http://www.zhihu.com/topic/19551762)

[乔布斯个人传记](http://www.amazon.cn/%E5%8F%B2%E8%92%82%E5%A4%AB%C2%B7%E4%B9%94%E5%B8%83%E6%96%AF%E4%BC%A0-%E6%B2%83%E5%B0%94%E7%89%B9%C2%B7%E8%89%BE%E8%90%A8%E5%85%8B%E6%A3%AE/dp/B00IM4IFL2/ref=sr_1_1?ie=UTF8&qid=1436592631&sr=8-1&keywords=%E4%B9%94%E5%B8%83%E6%96%AF)

### 拼写正确的重要性

有些面试官可能更注重细节，所以，拼写的单词一定要对，比如iOS，Xcode，iPhone，Objective-C，JSON等，良好的拼写习惯，会让面试官觉得你细心靠谱。

### Swift和Objective-C的比较

仁者见仁智者见智，从个人的使用角度上来看，Swift在某些情况上比Objective-C更加的严谨了，入门非常简单，但是想开发应用，还是需要学习cocoa框架，这玩意路子还是Objective-C的，所以有基础可能更好的理解Swift在iOS/Mac OS X 中的开发和应用。

[知乎原文](http://www.zhihu.com/question/24002984)

### 了解Watch OS

`Watch OS`是苹果公司推出的应用在手表上的一个操作系统，`Watch OS 1.0`需要跟iPhone相结合才能工作。

[Apple Watch](https://stratechery.com/2014/apple-watch-asking-saying/)

[Watch OS 2.0 开发概述](http://mp.weixin.qq.com/s?__biz=MjM5NTIyNTUyMQ==&mid=208847424&idx=1&sn=fac57c5da8136b07fe9cdf53d1ec9f4c#rd)

----

## iOS面试

<h5>property 后面可以有哪些修饰符</h5>

1. 读写修饰符 `readwrite` | `readonly`

    `readwrite` Xcode会帮助我们创建`setter`，`getter`方法，`readonly` Xcode只会帮助我们创建`getter`方法，不会创建`setter`方法。

2. `setter`相关的修饰符 `assign` | `retain` | `copy`

    2.1 `setter`相关的修饰符表明了`setter`方法该如何实现，`assign`用于基本数据类型`NSInteger`，`CGFloat`，C数据类型`int`，`float`，`id`类型等，这个符号不会涉及内存管理，但是如果是对象类使用了它，可能会导致内存泄漏或者`EXC_BAD_ACCESS`错误。

    2.2 `retain`用于对象类的内存管理，如果基本数据类型使用它，`Xcode`会直接报错。当对象类使用此修饰符时，`setter`方法的实现是先`release`一次，然后再对新的对象做一次`retain`操作。

    2.3 `copy`主要用于`NSString`，用于内容复制。

3. 原子性修饰符 `atomic` | `nonatomic`

    `atomic` 表示线程安全

    `nonatomic` 表示非线程安全，使用此修饰符会提高性能

4. `getter`，`setter`修饰符

    这两个修饰符用于设置生成的getter，setter的方法名

5. `strong`，`weak`修饰符（ARC）
在ARC中内存管理都只需要使用这两个修饰符，而且`strong`是默认全局的，只要你写了`Objective-C`的对象，不自己添加`weak`的话，默认就是`strong`。
    5.1 `strong`表示这个对象的拥有者
    一个对象可以有多个拥有者，`strong`就是用来表示对这个对象的拥有。比如在往`NSMutableArray`中添加`Objective-C`对象，当你从数组中删除时，这个对象并不会释放。需要你手动设置为`nil`，或者在控制器的生命周期内，由系统来释放。
    5.2 `weak`指针变量仍然可以指向一个对象，但不是这个对象的拥有者
    `weak`修饰的指针变量也可以指向对象，但不是这个对象的实际拥有者，也就是说`weak`修饰的指针变量如果想要释放，需要`strong`修饰的指针变量设置为`nil`，`weak`修饰的指针变量也会是一个`nil`，它指向的对象已经没有了，还需要设置`weak`修饰的指针变量为`nil`。
6. `nonnull` `nullable` `null_resettable`

Xcode 6.3推出的`nullability annotations`，主要是为了更好的Swift与Objective-C混编，在Swift中有可选型的概念`!`,`?`，但是Objective-C中木有这玩意，于是Xcode 6.3中才有了这个，
从字面可以看出:
    `nonnull` 表示对象不应该为空，如果是这个修饰符对应的就是Swift中已经解包的对象或者`!`
    `nullable`表示可以为`nil`或者`NULL`,对应是Swift中的可选`?`
    `null_resettable`则是表达属性的空属性，该属性`setter`访问器允许将其设置为`nil`（设置该属性为默认值），但是它的`getter`访问器不会提供一个`nil`值（因为它提供了默认值），有一个这样的属性如UIView’s tintColor，如果没有tint颜色指定时它会提供一个默认的tint颜色值，对应的Swift使用是var tintColor:UIColor!

## 实战
1. 使用 `weak` 关键字，相比 `assign` 有什么不同
    一般情况下使用`weak`是避免循环引用，因为它不是对象的拥有者。而`assign`则是用于基本数据类型，或者C类型，而且`assign`是直接赋值，可能会导致一个问题。比如我想a和b共用一块内存，a是用`assign`修饰的，`a = b`，现在a使用的目的已经完成，我想释放这个内存，但是a并不知道b到底用没用完，如果此时a释放内存，而b还在使用，那么会导致应用程序crash，使用`weak`就能避免这样的问题。
2. 怎么用 copy 关键字
    `copy`拷贝的是内容,`retain`是拷贝的指针
        * 以`string`为例,如果`string`的属性为`copy`的话,那么传入参数为`NSString`的话,即为不可变`string`,`retain`,`copy`效果一样.
        * 如果传入参数是`mutable`的话,那么`copy`拷贝内容,源随意变化不影响该属性的值.`retain`拷贝指针,源变化则属性值着变化,因为属性和源指向如何使用呢,通常在需要拷贝内容,但是副本和源不要互相影响的情况下使用.`*` 同一内存地址.
        * 例如`array/dictionary`中,可能会需要一个副本来做一些操作(筛选,排序等),但是并不希望影响原始值,则可以使用`copy`
3. @property (copy) NSMutableArray *array; 这样写有什么问题吗
    因为用了`copy`, 内部会深拷贝一次, 指针实际指向的是`NSArray`, 所以如果调用`removeObject`和`addObject`方法的话, 会`unRecognized selector`
4. 如何让自己的类用 copy 修饰符？如何重写带 copy 关键字的 setter？
    当一个对象发生改变时不影响另外一个对象，这里就需要使用`copy`关键字了，实现`NSCopying`协议，重写- `(id)copyWithZone:(NSZone *)zone`方法。
{% codeblock lang:objc %}
- (void)setName:(NSString *)name
{
    if(_name != name)
    {
        _name = [name copy];
    }
}
{% endcodeblock %}
5. @protocol 和 category 中如何使用 @property
    `@protocol`可以通过关键字:`@synthesize`或者在继承的类里面重新定义一次该属性(`extension`里面定义是不行的)
    `category`通过关联:`objc_setAssociatedObject`/`objc_getAssociatedObject`
6. `@property` 的本质是什么？`ivar`、`getter`、`setter` 是如何生成并添加到这个类中的
    `@property`本质是定义一个`objc_property`结构体
**如何生成目前不清楚**
7. `weak`属性需要在`dealloc`中置`nil`么
    不需要，因为weak会自动设置nil
8. `@synthesize`和@`dynamic`分别有什么作用
    关于@synthesize（现在已经不需要在写这个属性了，它是用来生成getter和setter方法）
    `@dynamic` 就是要告诉编译器`getter`和`setter`方法会在程序运行或者用到动态绑定的方式，以便让编译器通过编译，这个主要要在`NSManagerObject`上。
9. `ARC`下，不显式指定任何属性关键字时，默认的关键字都有哪些
    在默认情况下，所有的实例变量和局部变量都是`strong`类型的。
10. 用`@property`声明的`NSString`（或`NSArray`，`NSDictionary`）经常使用`copy`关键字，为什么？如果改用strong关键字，可能造成什么问题
因为不想改变了其中的值后把原来的值也跟着改变了，用了`strong`后会出现这样的状况。
11. 什么是ARC
    请阅读，然后随便谈谈你的理解即可。
    ARC是为了解决下面几个问题
    * 当我们要释放一个堆内存时，首先要确定指向这个堆空间的指针都被`release`了。（避免提前释放）
    * 释放指针指向的堆空间，首先要确定哪些指针指向同一个堆，这些指针只能释放一次。（`MRC`下即谁创建，谁释放，避免重复释放）
    * 模块化操作时，对象可能被多个模块创建和使用，不能确定最后由谁去释放。
    * 多线程操作时，不确定哪个线程最后使用完毕
[手把手教你ARC——iOS/Mac开发ARC入门和使用](http://onevcat.com/2012/06/arc-hand-by-hand/)
[理解 Objective-C 的 ARC](http://www.oschina.net/translate/objc-automatic-reference-counting-in-xcode-explained)
12. 请解释以下keywords的区别： `assign` vs `weak`, `block` vs `weak`
`assign`适用于基本数据类型，`weak`是适用于`NSObject`对象，并且是一个弱引用。
    * `assign`其实也可以用来修饰对象，那么我们为什么不用它呢？
    因为被`assign`修饰的对象在释放之后，指针的地址还是存在的，也就是说指针并没有被置为`nil`。如果在后续的内存分配中，刚好分到了这块地址，程序就会崩溃掉。
    * `weak`修饰的对象在释放之后，指针地址会被置为`nil`。所以现在一般弱引用就是用`weak`。
    * `block`是用来修饰一个变量，这个变量就可以在`block`中被修改，使用`block`修饰的变量在`block`代码快中会被`retain`（`ARC`下，`MRC`下不会`retain`） 
    * `weak`：使用`weak`修饰的变量不会在`block`代码块中被`retain`同时，在ARC下，要避免`block`出现循环引用 `weak typedof(self)weakSelf = self`
13. `__block`在`arc`和`非arc`下含义一样吗
    是不一样的，ARC会retain，非ARC不会。
14. 描述一个你遇到过的`retain` cycle例子
    在`viewController`中避免循环引用
{% codeblock lang:objc %}
[ downloadData:^(id responseData){
    _data = responseData;
}];
{% endcodeblock %}  
解决办法
{% codeblock  lang:objc %}
__weak ViewController *weakSelf = self;
[ downloadData:^(id responseData){
    weakSelf.data = responseData;
}];
{% endcodeblock %}
15. `+(void)load;` `+(void)initialize;`有什么用处
在Objective-C中，`runtime`会自动调用每个类的两个方法。`+load`会在类初始加载时调用，`+initialize`会在第一次调用类的类方法或实例方法之前被调用。这两个方法是可选的，且只有在实现了它们时才会被调用。
共同点：两个方法都只会被调用一次。
16. `UIView`和`CALayer`有什么关系
    * `UIView`是iOS界面元素的基础，所有的界面元素都继承于它。它本身是由`CoreAnimation`来实现的，它真正绘图的部分是由一个`CALayer`的类来管理的，`UIView`本身更像是一个`CALayer`的管理器。
    * `UIView`都存在一个`layer`属性，可以访问到`CALayer`的实例。
    * `UIView`的`CALayer`类也存在一个`view`树结构，可以像`UIView`一样进行添加
    * `UIView`的`layer`树在系统内部，由系统来维护，它存在着三棵树，分别是逻辑树，动画树，显示树
17. 如何高性能的给`UIImageView`加个圆角
    * 使用贝塞尔曲线来切割图片
    * 使用`Quartz2D`直接绘制图片
18. 使用`drawRect`有什么影响
`drawRect`方法依赖`Core Graphics`框架来进行自定义的绘制，但这种方法主要的缺点就是它处理`touch`事件的方式：每次按钮被点击后，都会用`setNeddsDisplay`进行强制重绘；而且不止一次，每次单点事件触发两次执行。这样的话从性能的角度来说，对`CPU`和内存来说都是欠佳的。
19. SDWebImage里面给UIImageView加载图片的逻辑是什么样的
详情看[最新版SDWebImage的使用](http://www.cnblogs.com/6duxz/p/4159572.html)
20. 麻烦你设计个简单的图片内存缓存器
图片的内存缓存，可以考虑将图片数据保存到一个数据模型中，所以在程序运行时这个模型都存在内存中，一定要具备移除策略，即释放数据模型。
21. 讲讲你用`Instrument`优化动画性能的经历
[怎么使用instrument](http://www.hrchen.com/2013/05/performance-with-instruments/)
22. `loadView`是干嘛用的
当你访问一个`ViewController`的`view`属性时，如果此时`view`的值是`nil`，那么，`ViewController`就会自动调用`loadView`这个方法。这个方法就会加载或者创建一个`view`对象，赋值给`view`属性。
`loadView`默认做的事情是：如果此`ViewController`存在一个对应的`nib`文件，那么就加载这个`nib`。否则，就创建一个`UIView`对象。
如果你用`Interface Builder`来创建界面，那么不应该重载这个方法。
如果你想自己创建`view`对象，那么可以重载这个方法。此时你需要自己给`view`属性赋值。你自定义的方法不应该调用`super`。如果你需要对`view`做一些其他的定制操作，在`viewDidLoad`里面去做。
[iOS 的loadView 及使用loadView中初始化View注意的问题](http://www.cnblogs.com/dyllove98/archive/2013/06/06/3123005.html)
23. 用过`CoreData`或者`SQLite`吗？读写是分线程的吗？遇到过死锁没？咋解决的
参考[CoreData与SQLite的线程安全](http://blog.csdn.net/hanangellove/article/details/44966769)
24. `GCD`里面有哪几种`Queue`？你自己建立过串行`queue`吗？背后的线程模型是什么样的
    * 主队列 `dispatch_main_queue();` 串行 ，更新UI
    * 全局队列 `dispatch_global_queue();` 并行，四个优先级：`background`，`low`，`default`，`high`
    * 自定义队列 `dispatch_queue_t queue;` 可以自定义是并行：`DISPATCH_QUEUE_CONCURRENT`或者串行`DISPATCH_QUEUE_SERIAL`
25. 为什么其他语言里叫函数调用， `Objective-C`里则是给对象发消息（或者谈下对`runtime`的理解）
网上关于`runtime`的资料非常多，其实这方面在平时的开发中使用非常非常之少，底层的黑魔法。
[Objective-C特性：Runtime](http://www.jianshu.com/p/25a319aee33d)
[Objective-C Runtime](http://tech.glowing.com/cn/objective-c-runtime/)
26. 什么是`method swizzling`
在Objective-C中调用一个方法，其实是向一个对象发送消息，查找消息的唯一依据是`selector`的名字。利用`Objective-C`的动态特性，可以实现在运行时偷换`selector`对应的方法实现，达到给方法挂钩的目的。
[详细的案例](http://blog.csdn.net/yiyaaixuexi/article/details/9374411)
27. runtime 如何实现 weak 属性 
{% codeblock  lang:objc %}
OBJC_ASSOCIATION_ASSIGN
OBJC_ASSOCIATION_RETAIN_NONATOMIC
OBJC_ASSOCIATION_COPY_NONATOMIC
OBJC_ASSOCIATION_RETAIN
OBJC_ASSOCIATION_COPY
objc_setAssociatedObject(self, &myKey, anObject, OBJC_ASSOCIATION_RETAIN);
{% endcodeblock %}
可以自定义`weak`来实现内存管理，Apple已经为我们准备了常量。
参考
[Associated Objects](http://nshipster.cn/associated-objects/)
[Objective-C Runtime 运行时之二：成员变量与属性](http://southpeak.github.io/blog/2014/10/30/objective-c-runtime-yun-xing-shi-zhi-er-:cheng-yuan-bian-liang-yu-shu-xing/)
28. `objc`中向一个`nil`对象发送消息将会发生什么
`objc`的特性是允许对一个 `nil` 对象发送消息不会 Crash，因为会被忽略掉。
29. 什么时候会报`unrecognized selector`的异常
调用一个不存在的方法
30. `objc`中向一个对象发送消息`[obj foo]`和`objc_msgSend()`函数之间有什么关系
{% codeblock lang:objc %}
[obj foo];
//编译时会变成
objc_msgSend(obj,@selector(foo));

[obj foo:parameter];
//编译时会变成
objc_msgSend(obj,@selector(foo:),parameter);
{% endcodeblock %}
31. 一个objc对象如何进行内存布局
可参考[Objective-C内存布局](http://www.cnblogs.com/csutanyu/archive/2011/12/12/objective-c_memory_layout.html)
32. 一个objc对象的isa的指针指向什么？有什么作用？
isa是一个Class 类型的指针. 每个实例对象有个isa的指针,他指向对象的类，而Class里也有个isa的指针, 指向meteClass(元类)。元类保存了类方法的列表。当类方法被调用时，先会从本身查找类方法的实现，如果没有，元类会向他父类查找该方法。同时注意的是：元类（meteClass）也是类，它也是对象。元类也有isa指针,它的isa指针最终指向的是一个根元类(root meteClass).根元类的isa指针指向本身，这样形成了一个封闭的内循环。
33. 下面的代码输出什么
{% codeblock  lang:objc %}
@implementation Son : Father
- (id)init
{
    self = [super init];
    if (self) 
    {
        NSLog(@"%@", NSStringFromClass([self class]));
        NSLog(@"%@", NSStringFromClass([super class]));
    }
    return self;
}
@end
{% endcodeblock %}
输出Son
34. runtime如何通过selector找到对应的IMP地址
    id (*IMP)(id, SEL, ...)
这个函数使用当前CPU架构实现的标准的C调用约定。第一个参数是指向self的指针(如果是实例方法，则是类实例的内存地址；如果是类方法，则是指向元类的指针)，第二个参数是方法选择器(selector)，接下来是方法的实际参数列表。
前面介绍过的SEL就是为了查找方法的最终实现IMP的。由于每个方法对应唯一的SEL，因此我们可以通过SEL方便快速准确地获得它所对应的IMP，查找过程将在下面讨论。取得IMP后，我们就获得了执行这个方法代码的入口点，此时，我们就可以像调用普通的C语言函数一样来使用这个函数指针了。
通过取得IMP，我们可以跳过Runtime的消息传递机制，直接执行IMP指向的函数实现，这样省去了Runtime消息传递过程中所做的一系列查找操作，会比直接向对象发送消息高效一些。

## Hybrid 混合开发
