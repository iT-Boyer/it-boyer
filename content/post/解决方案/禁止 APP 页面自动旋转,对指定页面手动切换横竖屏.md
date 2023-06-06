+++
title = "禁止 APP 页面自动旋转,对指定页面实现手动切换横竖屏"
description = "Test post to test another ox-hugo test."
date = 2021-05-20T08:20:00+08:00
publishDate = 2021-05-20T00:00:00+08:00
lastmod = 2021-05-31T10:08:49+08:00
tags = ["iOS"]
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## 项目遇到的实际问题 {#项目遇到的实际问题}

在实现 **单个页面支持横竖屏** 的需求,有两种解决方案.  
实际案例:在项目中,针对电子巡查有直播功能,需要支持全屏查看直播,同时支持录制直播画面的需求.  
为满足需求,电子巡查页面需要关于横竖屏适配的两点技术:  

1.  查看全屏直播,页面需要实现手动横竖屏切换功能
2.  全屏录制,页面在全屏录制时,需要禁用自动旋转


### 基于 info.plist 应用设置实现 {#基于-info-dot-plist-应用设置实现}

思路:通过应用级配置文件 `info.plist` 设置 app 横竖屏属性,如何在代码中实现对指定页面横竖屏的支持?需要借助 `Appdelegate` 协议方法 `application:supportedInterfaceOrientationsForWindow:` 通过代码来实现同等效果,然后扩展 `Appdelegate` 一个关于支持横竖屏属性,使得任意特定页面中,可以自由激活 App 横竖屏的支持.  

基于这个思路,以下是两个类的具体实现.  


#### 在 Appdelegage 设置 APP 支持方向 {#在-appdelegage-设置-app-支持方向}

1.  基于 Appdelgate 中的方法,添加新属性,来控制横竖屏  
    
    {{< highlight objc "linenos=table, linenostart=0, hl_lines=0 0" >}}
           //适配电子巡查横屏播放,录制禁止旋转:当VC处于横屏时,禁止自动旋转,且必须返回横屏方向
           @property (nonatomic, assign)BOOL hengStatus;
    {{< /highlight >}}
2.  在 `Appdelgate.m` 协议方法中添加适配代码  
    
    {{< highlight objc "linenos=table, linenostart=250, hl_lines=0-1 11-13" >}}
       - (UIInterfaceOrientationMask)application:(UIApplication *)application supportedInterfaceOrientationsForWindow:(UIWindow *)window {
           if (_allowRotate) {
               return UIInterfaceOrientationMaskAllButUpsideDown;
           } else {
               //解决个别界面横屏完在竖屏问题
               if (_hengPRotate)
               {
                   [AppDelegate forceLandscape];
       //            return UIInterfaceOrientationMaskLandscapeRight;
               }
               if (_hengStatus) {
                   return UIInterfaceOrientationMaskLandscapeRight;
               }
               return UIInterfaceOrientationMaskPortrait;
           }
       }
    {{< /highlight >}}


#### 在页面中激活横竖屏支持 {#在页面中激活横竖屏支持}

1.  在电子巡查实现类中 `PatrolOnlineViewController.m`  
    切换全屏方法: `clickFullScreenAction`.  
    实现了两个操作:手动横屏操作和监听横竖屏切换事件通知 `UIDeviceOrientationDidChangeNotification`  
    
    {{< highlight objc "linenos=table, linenostart=0, hl_lines=3-5" >}}
       - (void)clickFullScreenAction{
           [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(orientChange:) name:UIDeviceOrientationDidChangeNotification object:nil];
           //启动Appdelegate旋转屏幕适配
           AppDelegate * delegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
           delegate.allowRotate = YES;
           if ([[UIDevice currentDevice] respondsToSelector:@selector(setOrientation:)])
           {
               delegate.isHengStatus = !isFullPlay;
               if (isFullPlay == NO) {
                   [[UIDevice currentDevice] performSelector:@selector(setOrientation:) withObject:(id)UIInterfaceOrientationLandscapeRight];
               }else{
                   [[UIDevice currentDevice] performSelector:@selector(setOrientation:) withObject:(id)UIInterfaceOrientationPortrait];
               }
           }
       }
    {{< /highlight >}}
2.  旋转后,UI 的适配监听到屏幕旋转之后,针对横屏显示的两套 UI 布局样式,UI 更新完成之后,关闭横竖屏的支持,移除监听事件.  
    
    {{< highlight objc "linenos=table, linenostart=1, hl_lines=29-31 0" >}}
       - (void)orientChange:(NSNotification *)noti {
           UIDeviceOrientation orientation = [UIDevice currentDevice].orientation;
           CGRect customFrame;
           if (orientation == UIDeviceOrientationLandscapeLeft || orientation == UIDeviceOrientationLandscapeRight) {
               if(self.screenCAPManager == nil)
               {
                   //开启录屏windows
                   self.screenCAPManager = [QHScreenCAPManager createScreenCAPManager];
                   self.screenCAPManager.delegate = self;
               }
               //获取横屏布局frame
               customFrame = [UIApplication sharedApplication].keyWindow.bounds;
           }else{
               //恢复竖屏布局frame
               if (orientation == UIDeviceOrientationPortrait) {
                  customFrame = self.playerView.bounds;
               }
           }
           isFullPlay = !isFullPlay;
           //横竖屏适配
           _customPlayView.isFull = isFullPlay;
           _customPlayView.frame = customFrame;
           [_customPlayView setNeedsLayout];
           recordPlayView.frame = _customPlayView.bounds;
           //显示隐藏逻辑
           self.tableView.hidden = isFullPlay;
           _uploadBut.hidden = isFullPlay;
           //关闭横竖屏支持
           AppDelegate * delegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
           delegate.allowRotate = NO;
           [[NSNotificationCenter defaultCenter] removeObserver:self name:UIDeviceOrientationDidChangeNotification object:nil];
       }
    {{< /highlight >}}


### 基于容器控制器实现横竖屏 {#基于容器控制器实现横竖屏}

通过视图控制器或容器控制器实现单个页面横竖屏支持,针对这种实现方式,网上有很多详细教程,因为涉及到 `横竖屏事件的传递`,如果项目比较复杂时,理解其原理相当重要,推荐一篇图文介绍:[「iOS」终极横竖屏切换解决方案 - 知乎](https://zhuanlan.zhihu.com/p/28081039)  

项目中电子巡查 VC 视图控制器,需要在导航控制器并模态展示.基于 `横竖屏事件传递机制` ,想要导航控制器中的视图控制器支持横竖屏,需要封装一个重写两个方法(支持横竖屏)的导航控制器.  

根据电子巡查页面的两个需求,导航控制器要根据视图控制器的状态控制横竖支持.  
三个状态: **原始状态\*、 \*全屏状态** 、 **旋转状态**.  
>根据三种状态,横竖屏的方法的返回值逻辑:  
`shouldAutorotate` 方法:仅在旋转状态下返回 `true`, 原始/全屏状态返回 `false` 禁止自动旋转.  
`supportedInterfaceOrientations` 方法:默认返回竖屏. 在全屏状态下,返回横屏,确保录制过程保持横屏.  

实现如下:  
先声明一个 导航控制器子类,重写横竖屏支持的自定义方法: `AutorotateNavController` 继承 `UINavigationController`  

{{< highlight objc "linenos=table, linenostart=0, hl_lines=7-11 15-18" >}}
@implementation AutorotateNavController

- (void)viewDidLoad {
    [super viewDidLoad];
}

//是否支持自动旋转,在旋转过程中,支持自动旋转
-(BOOL)shouldAutorotate
{
    return isRotating;
}

//指定视图控制器支持的旋转方向
- (NSUInteger)supportedInterfaceOrientations {
    if(isFullScreen){
        return UIInterfaceOrientationMaskLandscapeRight;
    }else{
        return UIInterfaceOrientationMaskPortrait;
    }
}

@end
{{< /highlight >}}


## 参看文章 {#参看文章}

[iOS 强制横竖屏 Swift\_w13776024210 的博客-CSDN博客\_swift 强制横屏](https://blog.csdn.net/w13776024210/article/details/106399660)  
[「iOS」终极横竖屏切换解决方案 - 知乎](https://zhuanlan.zhihu.com/p/28081039)  
[objective c - setStatusBarHidden is deprecated in iOS 9.0 - Stack Overflow](https://stackoverflow.com/questions/31114340/setstatusbarhidden-is-deprecated-in-ios-9-0)  
[关于iOS横竖屏适配 - 简书](https://www.jianshu.com/p/1993144ea35e)  
[uiwebview不调用webViewDidFinishLoad的解决办法\_夺命红烧肉的专栏-CSDN博客](https://blog.csdn.net/liyunliyun08/article/details/51313235)  
[iOS最新StatusBar状态栏设置方式 - 简书](https://www.jianshu.com/p/552032b24472)  
[iOS UIViewController 无法关闭自动旋屏（自动旋转、手动旋转、兼容IOS6之前系统）\_jeffasd 的专栏-CSDN博客](https://blog.csdn.net/jeffasd/article/details/50173753)
