---
title: 整理iOS中几种常用的展示型视图控制器
date: 2017-02-16 17:53:18
updated: 2019-01-16 21:01:14
comments: true
tags: [转场]
categories:
- 项目总结
keywords: 动画,转场,UI
permalink: 
---
[开始项目](https://www.dropbox.com/s/4gj6levvlav2xzc/PresentationsDemoStart.zip?dl=0)
[完整项目](https://github.com/appcoda/Presentation-Controllers-Demo)

## UIAlertController
在iOS8中，提供`UIAlertController`控制器代替`UIAlertView`和`UIActionSheet`两个控件。给用户展示提示信息的新的一种方式。

### 优势
1. 能够自适应的（在iPad上，an `action sheet` style alert will present itself in a popover），
2. 显示方式：可以轻松切换`Action sheets`和`alert view`两种显示样式`alert view`被以modal态显示presenting视图控制器上，`Action sheets`被固定在以屏幕底部。 
3. 按钮事件实现：使用闭包的方式来处理，相较之前通过实现代理的方式要简单很多。
4. 子控件支持：`Alert view`支持按钮和输入框两种，Action sheets仅支持按钮一种控件。
5. 不同于以往的两类`UIAlertController`继承自`UIViewController`。这意味着可以使用视图控制器提供展示信息的功能。

### 创建使用UIAlertController
用`title`，`message`参数来实例化`alertController`实例，然后在实例中添加两个闭包的按钮
{% codeblock showAlertWasTapped lang:swift %}
@IBAction func showAlertWasTapped(sender: UIButton) 
{
    let alertController = UIAlertController(title: "Appcoda", message: "Message in alert dialog", preferredStyle: UIAlertControllerStyle.Alert)

    let deleteAction = UIAlertAction(title: "Delete", style: UIAlertActionStyle.Destructive, handler: {(alert :UIAlertAction!) in
        println("Delete button tapped")
    })
    alertController.addAction(deleteAction)

    let okAction = UIAlertAction(title: "OK", style: UIAlertActionStyle.Default, handler: {(alert :UIAlertAction!) in
        println("OK button tapped")
    })
    alertController.addAction(okAction)

    presentViewController(alertController, animated: true, completion: nil)
}
{% endcodeblock %}
运行效果：

### UIAlertControllerStyle枚举：`Alert`切换`ActionSheet`
在`UIAlertController`之前，切换`alert`和`action sheet`需要重写大量的代码，但现在只需要改变一个枚举值`UIAlertControllerStyle.Alert`为`UIAlertControllerStyle.ActionSheet`.
{% codeblock UIAlertControllerStyle.ActionSheet lang:swift %}
let alertController = UIAlertController(title: "Appcoda", message: "Message in alert dialog", preferredStyle: UIAlertControllerStyle.ActionSheet)
{% endcodeblock %}
在iPhone上，屏幕底部显示一个`action sheet`。
问题：在iPad上，点击上面的按钮崩溃，需要定义锚点位置。

### popoverPresentationController锚点：sourceView/sourceRect
`popover controller`在`alertController`视图内展示，需要一个`popover箭头`指向`alertController`视图的某一位置。
通过设置`sourceView`来确定`popover箭头`位置，这个`popover`以及`popover箭头`指向的矩形区域都在这个`sourceView`上。
在调用`presentViewController()`之前添加代码：
{% codeblock lang:swift %}
alertController.popoverPresentationController?.sourceView = view
alertController.popoverPresentationController?.sourceRect = sender.frame
{% endcodeblock %}

## UIPopoverPresentationController
`Alert`主要用于显示用户的提示信息，当展示的信息很多时，就需要借助`popover presentation controller`。

### 在compact和regular两种屏幕中显示模态视图
在`storyboard`文件，设置视图的`storyboard ID`:`PopoverViewController`，设置模态视图展示样式，展示在`compact-width`和`regular-width`的两种设备屏幕上。
实现如下：
{% codeblock actionWasTapped lang:swift %}
@IBAction func actionWasTapped(sender: UIBarButtonItem) 
{
    let storyboard : UIStoryboard = UIStoryboard(name: "Main", bundle: nil)
    let vc = storyboard.instantiateViewControllerWithIdentifier("PopoverViewController") as! UIViewController
    vc.modalPresentationStyle = UIModalPresentationStyle.Popover
    let popover: UIPopoverPresentationController = vc.popoverPresentationController!
    popover.barButtonItem = sender  //`popover箭头`锚的位置
    presentViewController(vc, animated: true, completion:nil)
}
{% endcodeblock %}

#### 设置锚点四种方式
1. barButtonItem
先获取该视图控制器的`popoverPresentationController`控制器，通过`popover`控制器的`barButtonItem`属性来设置锚点控件。当弹出时`popover箭头`就指向这个barButtonItem控件。
2. 通过指定`sourceView`和`sourceRect`两个属性，就像前面例子中一样来指定锚点位置。
3. 通过其他属性来实现，例如：`permittedArrowDirections`，也能够指定锚点。
4. 如果在在展示过程中，无法确定箭头的方向时，就是用默认值：`UIPopoverArrowDirection.Any`.
在iPad显示：

在iPhone上以模态显示：


### 在iPhone设备上dissmiss模态视图
要在iPhone设备上，实现模态视图dissmiss功能，需要借助导航控制器，同时这个模态视图需要遵循`UIPopoverPresentationController`协议，实现两个代理方法
#### 实现`UIPopoverPresentationController`协议
1. `PopoverViewController`类定义修改如下:
{% codeblock lang:swift %}
class PopoverViewController: UIViewController, UIPopoverPresentationControllerDelegate {
{% endcodeblock %}
2. 在actionWasTapped()函数中调用`presentViewController()`之前添加：
{% codeblock lang:swift %}
popover.delegate = self
{% endcodeblock %}
#### 方法一：返回自适应设备的视图展示样式
当APP在`compact-width`设备上弹出一个视图时调用.这个方法告诉OS系统使用的视图展示样式。
这里OS系统被告知在`compact-width`设备上，使用全屏的样式展示视图。
{% codeblock adaptivePresentationStyleForPresentationController() lang:swift %}
func adaptivePresentationStyleForPresentationController(controller: UIPresentationController) -> UIModalPresentationStyle 
{
    return UIModalPresentationStyle.FullScreen
}
{% endcodeblock %}
#### 方法二：返回自定义的视图控制器
当前展现的视图和原来的展示方式不同时调用.我们设置这个视图的`Popover presentation`展示方式，但是我们指定在`compact-width`设备上，这样它会以full screen样式展示。在这个函数中，样式切换发生时，会return自定义的视图控制器。
{% codeblock presentationController(_:viewControllerForAdaptivePresentationStyle) lang:swift %}
func presentationController(controller: UIPresentationController, viewControllerForAdaptivePresentationStyle style: UIModalPresentationStyle) -> UIViewController? 
{
    let navigationController = UINavigationController(rootViewController: controller.presentedViewController)
    let btnDone = UIBarButtonItem(title: "Done", style: .Done, target: self, action: "dismiss")
    navigationController.topViewController.navigationItem.rightBarButtonItem = btnDone
    return navigationController
}
{% endcodeblock %}

#### `Done`按钮的dismiss事件
在导航控制器中国封装这个视图，在导航条上添加一个`Done`按钮，点击`Done`dismiss这个视图
{% codeblock dismiss lang:swift %}
func dismiss() 
{
    self.dismissViewControllerAnimated(true, completion: nil)
}
{% endcodeblock %}

在iPhone上，显示修改后的视图控制器，多出带按钮的导航栏。
在iPad上，视图控制器显示没有导航控制器，因为它不使用全屏幕显示。
如果想让iPhone像iPad一样显示一个Popover，只`adaptivePresentationStyleForPresentationController`返回：
{% codeblock lang:swift %}
return UIModalPresentationStyle.None
{% endcodeblock %}
