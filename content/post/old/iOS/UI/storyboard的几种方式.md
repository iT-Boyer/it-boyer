---
title: storyboard的几种方式
date: 2018-06-20 14:49:37
updated: 2018-07-03 12:30:31
comments: true
tags: [iOS]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## 通过IB（xib/storyboard）创建View的周期方法
1. `loadView`：加载View方法，UI是通过代码绘制时，初始化控制器的视图时，会调用该方法。优先级高于IB视图，当重载时，会直接覆盖IB中的视图,因为无论nib也好，xib也好，最终在执行UIViewController生命周期函数`loadView`之前，都会转化成可执行的nib文件。
2. `initWithNibName`：是类的构造器方法，通过IB创建的类：简称`IB类`，`IB类`需要通过这个构造器来实例化对象。
3. `initWithCoder`：当`IB类`实例化时会调用该方法，即通过`initWithNibName`构造器实例化对象时，会调用该方法来分配`IB对象`的内存空间。
4. `awakeFromNib`：当实例化`IB视图类`时执行，即当IB文件被加载的时候，会发送一个`awakeFromNib`的消息到IB文件中的每个的对象，每个对象都可以定义自己的awakeFromNib函数来响应这个消息，执行一些必要的操作。
>帮助记忆：一开始经过`initWithCoder`创建出来的控件是死的，然后通过`awakeFromNib`来唤醒，所以这会有一个先后的调用顺序
5. `viewDidLoad`：当view对象被加载到内存后就会执行viewDidLoad，所以不管通过nib文件还是代码的方式创建对象都会执行viewDidLoad 。

## 加载xib方法
### 加载视图
#### 方法一
```objc
 NSArray* nibView =  [[NSBundle mainBundle] loadNibNamed:@"xibfileName" owner:nil options:nil];
 UIView *xibView = nibView.lastObject;
//=======
 // 这里的bundle参数是nil,(这里nil默认就是mianBundle)
 UINib *nib = [UINib nibWithNibName:@"xib文件名" bundle:nil];
 NSArray *views = [nib instantiateWithOwner:nil options:nil];
```
#### 方法二
`owner`:xib中的fileObject参数
```objc
//加载所有xib文件
NSArray* objects = [[NSBundle mainBundle] loadNibNamed:nibName owner:self options:nil];

//加载指定xib文件
ContactsTableViewCell  *cell = [[[NSBundle mainBundle]loadNibNamed:@"ContactsTableViewCell" owner:nil options:nil] objectAtIndex:0];
```
### 加载控制器对象
```objc
self = [super initWithNibName:@"xibName" bundle:nibBundleOrNil];
```

## 加载storyboard
### 加载控制器对象
```objc
// 加载storyboard
UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Two" bundle:nil];

// 创建storyboard里面灰色的控制器
　　　//找到shtoryboard里面设置的初始控制器
　　　//    UIViewController *vc = [storyboard instantiateInitialViewController];
　　　　　　　　
　　　　　　　// 从storyboard里面找出绑定标识的控制器
　　　　　　　MJTwoViewController *vc = [storyboard instantiateViewControllerWithIdentifier:@"pink"];
　　　　　　　self.window.rootViewController = vc;
```


