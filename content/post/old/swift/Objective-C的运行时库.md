---
title: Objective-C的运行时库
date: 2017-09-25 20:01:36
updated: 2018-11-10 09:14:55
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
---
## Objective-C 运行时
Objective-C 是一门基于运行时的编程语言，这意味着所有方法、变量、类之间的链接，都会推迟到应用实际运行的最后一刻才会建立。这将给开发人员极高的灵活性，因为我们可以修改这些链接。而不同的是，Swift 绝大多数时候是一门面向编译时的语言。因此在 Swift 当中，灵活性受到了限制，不过您会因此得到更多的安全性。

### runtime.h开源库
Objective-C 的运行时本质上是一个库。它负责了 “Objective” 这个部分，因此您所知、所爱的面向对象编程，都是在这里实现的。如果您想要访问里面的函数的话，只需要导入这个库即可：
```objc
#import <objc/runtime.h>
```
`runtime.h`开源库主要由 C 和汇编编写而成，其实现了诸如类、对象、方法调度、协议等面向对象编程这个部分。

#### 成员结构体
在运行时中`对象`和`类`本质上是一个非常简单的结构体，在运行时环境下，我们就可以创建，读取，修改这些属性方法等，例如：使用`allocateClassPair`函数创建类。
1. **对象结构体**
对象结构体中仅提供一个**`isa`**属性，是关联`类引用`的指针。这也就是 Objective-C 当中的所有对象都需要实现的。
在 `runtime.h` 当中对象的定义：
```objc
typedef struct objc_class *Class;
struct objc_object {
    Class isa;
};
```
2. **类结构体**
```objc
struct objc_class {
    Class isa;
    Class super_class;
    const char *name;
    long version;
    long info;
    long instance_size;
    struct objc_ivar_list *ivars;
    struct objc_method_list **methodLists;
    struct objc_cache *cache;
    struct objc_protocol_list *protocols;
};
```
**isa**属性：建立自身与 `super_class` 这个值进行关联。
**super_class**:除了 NSObject 这个类之外，super_class 的值永远不会为 nil，因为 Objective-C 当中的其余类都是以某种方式继承自 NSObject 的。
**ivars**：变量列表，**methodLists**：方法列表，**protocols**：协议列表，其他属性：`name`、`version`、`info` 之类的值。  

3. **变量结构体**
包含了变量类型和变量名称。偏移量 (offset) 则是内存管理方面的内容。
```objc
struct objc_ivar {
    char *ivar_name;
    char *ivar_type;
    int ivar_offset;
}
```
4. **方法结构体**
```objc
struct objc_method {
    SEL method_name;
    char *method_types;
    IMP method_imp;
}
```
**method_name**： 方法名，使用`Selector`来表示方法编号，对应在 `performSelector` 当中所匹配的内容。
**method_types**：方法类型，使用`char`字符来表示。
**method_imp**：方法的实现，`IMP`是一个函数指针，方法实现的一种特定的表示方式，是方法混淆特性的根本所在。

#### 成员函数 
运行时库提供一系列运行时函数，实现在运行时动态的对成员结构体（类/对象）进行创建，修改等相关操作，例如：创建类，在`类别`中添加存储属性
##### 动态创建运行时类
在制作库框架会大量运用使用到运行时函数。如果您无法知道用户将会创建什么样的数据，那么您就需要在运行时进行类的创建了。Core Data 就使用了这个功能。此外，如果您愿意的话，它还可以用在 JSON 解析当中。
类的创建要用的 Objective-C 两个运行时函数：`allocateClassPair`和`objc_registerClassPair`
```objc
//类函数的构造器
Class myClass = objc_allocateClassPair([NSObject class], "MyClass", 0);
// 在这里添加变量、方法和协议
objc_registerClassPair(myClass);
// 当类注册之后，变量列表将会被锁定
[[myClass alloc] init];   //可见这个运行时类和Objective-C创建的类毫无区别
```
**[NSObject class]**：就是`类结构体`的属性**isa**要关联的类引用
**"MyClass"**：指定`类结构体`的**name**属性值
额外字节的定义：通常我们都直接赋值 0 即可
2. 添加变量、方法以及协议
3. `registerClassPair`注册这个 ClassPair,注册之后，我们就无法修改变量列表了，不过其余的内容仍然可以修改。

#####  为类别中新增存储属性
类别可以在既有的类中添加函数、计算属性，无法添加存储属性。但是在运行时环境下，可以借助 `setAssociatedObject` 和 `getAssociatedObject`实现向既有的类当中添加存储属性。
例如在`NSObject`新增一个`Name`存储属性：
```
@implementation NSObject (Name)
@dynamic Name;
- (void)setName:(id)object {
    objc_setAssociatedObject(self, @selector(Name), object,
    OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}
- (id)Name {
    return objc_getAssociatedObject(self, @selector(Name));
}
```
##### 内省机制
内省机制是用来判别这个类是否实现具备某项功能。当我们使用了一个带有可选方法的协议时，为了避免崩溃发生，可以借助这个**内省机制**来判断这个对象是否可以调用此可选方法。
内省机制提供了两个运行时函数
`isMemberOfClass`: 对比两者的 **isa** 是否相同。
`respondsToSelector`:则封装了一个运行时函数：`class_respondsToSelector`，两个参数`类`和 `Selector` 
```objc
//类成员判断
[myObject isMemberOfClass:NSObject.class];
//类方法判断
[myObject respondsToSelector:@selector(doStuff:)];
//等价上一句
class_respondsToSelector(myObject.class, @selector(doStuff:));
```
##### 使用运行时实现单元测试
当我们在编写 `XCTestCase` 的时候，需要完成 `setUp` 和 `tearDown` 的设定，随后才能编写相关的 `test` 函数。当测试运行的时候，系统会自行遍历所有的测试函数，并自动运行。
```
unsigned int count;
Method *methods = class_copyMethodList(myObject.class,&count);  //方法列表
//Ivar *list = class_copyIvarList(myObject.class,&count);       //变量列表

for(unsigned i = 0; i < count; i++) {
    SEL selector = method_getName(methods[i]);                  //获取到方法名
    NSString *selectorString = NSStringFromSelector(selector);  //转为字符串
    if ([selectorString containsString:@"test"]) {
        [myObject performSelector:selector];
    }
}
free(methods);
```
单元测试的原理就是借助了运行时函数`class_copyMethodList`获取到方法名，然后将其转换为字符串，检查其是否包含有 “test”，如果有便可以运行。
##### 运行时方法调度
动态的向对象当中添加方法并调用新增的方法。方法转发，方法混淆：替换或交换
1. **动态的为类新增方法**
了解到运行时的方法的结构体组成：方法名，SEL和`IMP`实现，需要三个运行时函数来新建一个运行时方法
```objc
//
Method doStuff = class_getInstanceMethod(self.class, @selector(doStuff));
//获取方法的实现
IMP doStuffImplementation = method_getImplementation(doStuff);
//获取方法的类型
const char *types = method_getTypeEncoding(doStuff); //“v@:@"

class_addMethod(myClass.class, @selector(doStuff:), doStuffImplementation, types);
```
`class_getInstanceMethod`:获取方法的`SEL`
`method_getImplementation`:方法的实现`IMP`
`method_getTypeEncoding`: 获取方法的类型，char字符表示
`class_addMethod`: 向对象当中添加方法的运行时函数。它所需的参数，即上述方法结构体当中的那三个值：Selector、方法实现和方法类型。

2. **调用运行时方法**
我们可以使用 `[self doStuff]` 或者`[self performSelector:@selector(doStuff)]`来进行调用。
实际上在运行时级别，它们都是借助 `objc_msgSend` 向对象发送了一个消息：
```objc
objc_msgSend(self, @selector(message));
```
但是如果调用方法所在的对象为 nil 的时候，我们就会得到一个异常，应用便会崩溃。但事实证明，在崩溃之前会预留几个步骤，从而允许我们对某个不存在的函数进行一些操作：方法转发/替换等。

3. 方法转发
当桥接两个不同的框架的时候，可以将方法转发给其它目标，或者，当我们调用某个未实现的方法时，运行时有如下处理步骤：
```
// 1添加实例方法/类方法，如果 return YES，就会调用原始方法
+(BOOL)resolveInstanceMethod:(SEL)sel;
+(BOOL)resolveClassMethod:(SEL)sel;
// 2 返回可以处理 Selector 的对象
- (id)forwardingTargetForSelector:(SEL)aSelector;
// 3 创建 NSInvocation
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector;
// 4 在您所选择的目标上调用 Selector
- (void)forwardInvocation:(NSInvocation *)invocation {
    [invocation invokeWithTarget:target];
}
```
3.1. 首先调用两个类方法：一个名为 `resolveInstanceMethod`/`resolveClassMethod`类方法，这时候我们便有机会来添加方法了，如果我们返回了 YES，就意味着原始方法将会再次被调用。
3.2.  `forwardingTargetForSelector`：当不要添加新方法时，可以直接返回需要调用方法的目标对象，之后这个对象就会调用 Selector。
3.3. `forwardInvocation`：实现目标对象调用 Selector，所有的调用过程都被封装到 `NSInvocation` 对象当中。需要 通过`methodSignatureForSelector`函数创建。

4. 动态特性方法混淆：替换或交换
方法混淆是通过 `class_replaceMethod` 或者 `method_exchangeImplementations` 实现方法的替换。常用于日志记录和 Mock 测试。
当类加载之后，会调用一个名为 `load` 的类函数。由于我们只打算混淆一次，因此我们需要使用 `dispatch_once`。接着我们便可以得到该方法，然后使用 `class_replaceMethod` 或者 `method_exchangeImplementations` 来替换方法。
```objc
+ (void)load {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        Class class = [self class];

        SEL originalSelector = @selector(doSomething);
        SEL swizzledSelector = @selector(mo_doSomething);

        Method originalMethod = class_getInstanceMethod(class,
        originalSelector);
        Method swizzledMethod = class_getInstanceMethod(class,
        swizzledSelector);

        BOOL didAddMethod = class_addMethod(class, originalSelector,
                                method_getImplementation(swizzledMethod),
                                method_getTypeEncoding(swizzledMethod));

        if (didAddMethod) {
            class_replaceMethod(class,swizzledSelector,
                                method_getImplementation(originalMethod),
                                method_getTypeEncoding(originalMethod));
        } else {
            method_exchangeImplementations(originalMethod, swizzledMethod);
        }
    });
}
```
