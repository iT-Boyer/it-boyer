---
title: UIStackView教程了解StackView
date: 2017-02-24 18:12:14
updated: 2019-01-16 21:01:13
comments: true
tags: [API]
categories:
- 学习笔记
keywords: iOS
permalink: 
---
[原地址](http://blog.csdn.net/kmyhy/article/details/50456444)
## UIStackView简单介绍
一个Stack View能够将它所含的View以各种方式沿其轴向进行分布，同时也可以将View沿某个方向等距分布，要隐藏`Stack View`中的视图，你只需要设置该View的`Hidden`属性为`true`，剩下的工作Stack View会自己完成。。
坐标（Axis）、间隔（Spacing）、对齐（Alignment）以及分布（Distribution ）等。
distribution属性：决定Stack View如何沿它轴向的水平方向摆放它的subview，当属性值是Fill，这表示subview会沿轴向完全占据Stack View。因此，Stack View会拉伸其中一个subview使其填充剩余空间，尤其是水平内容优先级最低的那个，如果所有subview优先级相同，则拉伸第一个subview。
Alignment属性：决定了Stack View如何沿它轴向的垂直方向摆放它的subview，对于一个垂直的Stack View，这个属性可以设置为Fill、Leading、Center和Trailing。
对于水平的Stack View，这个属性则稍有不同：
.Top取代了.Leading，.Bottom取代了.Trailing。此外，水平Stack View还多出了两个属性值：.FirstBaseLine和.LastBaseLine。
对于水平的Stack View，这个属性则稍有不同：
Fill:
Leading:
Center:
Trailing:

布局方式对比

### 添加一个新的Stack View
点击故事板画布左下角`Auto Layout工具栏`中的`Stack按钮`：
另一种解散的方法是选中Stack View，然后点击`Editor\Emebed in\stack view`菜单。

### 解散一个废弃的Stack View
首先，选定想解散的Stack View。按下`Option键`，点击`Stack 按钮`。这将弹出一个上下文菜单，然后点击Unembed：
另一种解散的方法是选中Stack View，然后点击`Editor\Unemebed`菜单。

## 使用场景描述
当APP运行中增加或删除一个`view`时，需要重新调整邻近`view`的位置布局。
预见困境：
1. 在故事板中新建一些布局约束连接，以便能够安装或卸载其中的一些约束
2. 使用第三方库来实现
3. 根据任务复杂程度完全用代码实现
也许这个在视图附近的视图树中的所有View都不需要在运行时改变，但当你将新视图添加到故事板时，仍然要想方设法为它挤出空间来。

`UIStackView`提供了一个对多个视图进行水平或垂直布局的方法。通过对几个属性进行简单设置，比如对齐、分布和间距，可以让我们让其所包含的视图适应于其有效空间。

## 实现需求
存在着这几方面的问题:
### 问题一：自适应横竖屏
在横屏状态下（command+左箭头旋转为横屏），发现截图中的一排按钮位置无法适应屏幕宽度的变化。这时可以使用`UIStackView`来帮助实现自动适应横竖屏的效果。

### 问题二：控件间留白不紧凑
点击WEATHER旁边的Hide按钮。隐藏了下面的文本内容后，留下了一大块的空白区域。

### Storyboard引入界面
打开`Main.storyboard`，找到`Spot Info View Controller`这个Scene。将这些标签和按钮设为不同的背景色，是为了在运行时效果更直观。就是在故事板中，这也有助于看到`Stack View`属性的改变导致其内部视图的变化。
如果想在运行App时看见这些颜色，在`SpotInfoViewController`的`viewDidLoad()`方法中将下列语句注释：
{% codeblock swift lang:swift %}
// 清空标签和按钮的背景色
for view in backgroundColoredViews 
{
    view.backgroundColor = UIColor.clearColor()
}
{% endcodeblock %}
### Size类便于使用storyboard
在本案例中，故事板中Scene大小不是默认`600x600`，在这里Simulated Metrics下的Size属性被设置成iPhone 4-inch。Simulated Metrics属性在运行时并没有任何影响——不同设备上视图的大小仍然会自动改变。

## 创建水平`UIStackView`

### 按钮模块
1. 选中按钮
用`Command+左键`同时选中`Spot Info View Controller`底下一排的所有按钮：
2. Stack按钮添加一个新的Stack View
点击故事板画布左下角`Auto Layout工具栏`中新增的`Stack按钮`：    


这些按钮被嵌到一个新的Stack View中：


#### 给新的Stack View添加布局约束
要在故事板选取一个充满了子视图的Stack View还是比较难的，介绍两种选择技巧。
3.1 在`Outline视图`中选取Stack View


3.2 Shift+右键调出View树
在Stack View 的任意地方按下`Shift+右键`或者`Control+Shift+左键`（如果你正在用触控板的话）。这时将弹出一个上下文菜单，列出了位于所点击的地方的View树，你可以在这个菜单中选择Stack View。


4. 自动布局工具栏中的Pin按钮,添加一个约束

首先勾选Constrain to margins。然后在Stack View四周添加下列约束：
{% codeblock swift lang:swift %}
Top: 20, Leading: 0, Trailing: 0, Bottom: 0
{% endcodeblock %}
仔细检查top、leading、trailing、bottom中的数字并确保它们的I型柱都被选中。然后点击Add 4 Constraints：

#### 按钮等间距分布
添加约束后，导致第一个按钮被拉伸：
##### 使用等宽约束的`Spacer View`实现
解决这个问题只能使用空白的View来分隔这些按钮，在按钮之间摆放上一些用于分隔空间的 Spacer View。所有的Spacer View都要添加等宽约束，以及许多额外的约束，才能将这些Spacer View布局正确。
这看起来如下图所示。为了直观起见，这些Spacer View的背景色设置成了浅灰色：
如果要在运行时添加一个按钮或者隐藏/删除一个按钮时，要想调整这些Spacer View和约束就要命了。

##### `Distribution`属性：沿轴向水平分布
distribution属性：决定Stack View如何将它的subview沿轴向分布，当属性值是Fill，这表示subview会沿轴向完全占据Stack View。因此，Stack View会拉伸其中一个subview使其填充剩余空间，尤其是水平内容优先级最低的那个，如果所有subview优先级相同，则拉伸第一个subview。
打开Stack View属性面板。将`Distribution`属性由`Fill`修改为`Equal Spacing`：
编译运行，点击某个单元格，旋转模拟器（⌘→）。你将看到最下一排按钮现在按照等间距排列了！

### Rating版块
选中RATING标签，以及旁边的显示为几个星形图标的标签：
然后点击Stack按钮将它们嵌到一个Stack View中：
然后点击Pin按钮。勾选Constrain to margins，并添加如下约束：
{% codeblock swift lang:swift %}
Top: 20, Leading: 0, Bottom: 20
{% endcodeblock %}
打开属性面板，将间距设置为8：
你可能会看到一个 Misplaced Views的布局约束警告，同时星星标签会显示将会被拉伸到视图之外：
有时候Xcode会临时提示一些警告，或者显示Stack View的位置不正确，这些警告会在你添加其他约束后消失。你完全可以忽略这些警告。
要解决这个警告，我们可以修改一下Stack View的Frame然后又改回，或者临时修改它的一条布局约束。
让我们试一下。先将Alignment 属性从Fill修改为Top，然后又改回原来的Fill。你将看到这下星星标签显示正常了：
编译运行，进行测试

## 创建垂直的Stack View
Xcode会自动根据这两者的位置推断出这将是一个垂直的Stack View，Stack View没有添加任何约束时，会自动适应了两个标签中的最宽的一个的宽度。

### WHY VISIT模块
选中WHY VISIT标签及下面的标签,创建一个垂直的Stack View：
点击Stack 按钮将二者嵌到一个Stack View：

#### 添加约束
默认，约束是相对于距离最近的对象，对于Bottom约束来说就是距离它15像素的Hide按钮。但我们其实是想让约束相对于WEATHER标签。
选中Stack View，点击Pin按钮。勾选Constrain to margins，设置Top、Leading、Trainling为0。
然后，点击Bottom右边的下拉按钮，从列表中选择WEATHER（curent distance =20）：
最后点击Add 4 Constraints按钮。显示结果如下图所示：

#### alignment属性：轴向的垂直方向
Stack View问题，它的右边对齐于View的右边。但是底下的标签仍然是原来的宽度。需要使用alignment属性解决这个问题。
当你测试完所有Alignment值的布局效果后，将Alignment修改为Fill：
将`Alignment`设置为`Fill`，表示所有View将沿与Stack View轴向垂直的方向进行全占式分布。这会让WHY VISIT标签扩展它的宽度到100%.

如果我们只想让底下的标签将宽度扩展到100%怎么办？

这个问题现在看来还不是多大的问题，因为两个标签在运行时的背景色都是透明的。但对于Weather版块来说就不同了。

我们将用另外一个Stack View来说明这个问题。

## 垂直／水平Stack View嵌套使用

### Weather版块
在Weather版块相对复杂一些，因为它多了一个Hide按钮。
要隐藏`Stack View`中的视图，你只需要设置该View的`Hidden`属性为`true`，剩下的工作Stack View会自己完成。这也是我们解决用户隐藏WEATHER标签下文本的主要思路。
一种方法是使用嵌套的Stack View，先将WEATHER标签和Hide按钮嵌到一个水平StackView，再将这个Stack View和标签嵌到一个垂直Stack View。

#### 垂直stackView
注意Alignment属性负责Stack View轴向垂直的方向上的布局。所以，我们需要将Alignment属性设置为 Bottom：

#### 水平StackView中出现按钮拉伸标签的问题
注意，WEATHER标签被拉伸为和Hide按钮一样高了。这并不合适，因为这会导致WEATHER标签和下面的文本之间多出了一些空间。
正确的方法是让 Hide 按钮不要和 Weather 版块呆在同一个Stack View中，或者任何别的Stack View中。
这样，在顶层View中还会保留一个subview，你将为它添加一个相对于WEATHER标签的约束——WEATHER标签嵌在Stack View里的。也就是说，你要为位于Stack View之外的按钮加一个约束，这个约束是相对于Stack View内的一个标签！

#### 垂直stack View1:嵌套WEATHER标签和标签
选中WEATHER标签和标签：
点击 Stack 按钮：
点击Pin 按钮，勾上Constrain to margins，然后添加如下约束：
{% codeblock swift lang:swift %}
Top: 20, Leading: 0, Trailing: 0, Bottom: 20
{% endcodeblock %}
将Stack View的Alignment设为Fill：
我们需要在 Hide 按钮左边和WEATHER标签右边加一条约束，这样WEATHER 标签的宽度就不会拉满整个Stack View了。

当然，底下的标签宽度还是需要100%占满的。

我们是通过将WEATHER标签嵌到一个垂直Stack View 来实现的。注意，垂直Stack View的Alignment 属性可以设置为 .Leading，如果将Stack View拉宽，则它里面的View 会保持左对齐。

#### 垂直stack View2: 仅嵌套WEATHER标签
从Outline视图中选取WEATHER 标签，或者用Control+Shift+左键的方式选取WEATHER 标签：
然后点击Stack 按钮：
确保Axis 为 Vertical 的情况下，将Alignment 设置为 Leading：

#### 按钮和WEATHER标签两个约束
从Hide 按钮用右键拖一条新的约束到 WEATHER 标签：
按下Shift键，同时选择Horizontal Spacing 和 Baseline。然后点击 Add Constraints：
编译运行。Hide 按钮的位置现在对了，而且当按下Hide 按钮，位于Stack View 中的标签被隐藏后，下面的视图也会被调整——根本不需要我们进行手动调整。

## 顶级 Stack View
在Outline 视图中，用Command+左键选择5个最顶级的 Stack View：
然后点击 Stack 按钮：
点击Pin 按钮，勾上 Constrain to margins，将 4 个边的约束都设为0。然后将Spacing 设置为20，Alignment 设为 Fill。现在故事板会是这个样子：
编译运行：
噢！这个 Hide 按钮又失去了它 的约束！因为包含 WEATHER 标签的Stack View的外边又套了一层 Stack View。这不是什么大问题，就像之前你做过的那样，再重新为它添加约束就是了。

右键从Hide 按钮拖一条约束到 WEATHER标签，按下 Shift 键，同时选择 Horizontal Spacing 和 Baseline。然后点击 Add Constraints：

## 重新调整视图位置
现在，所有的版块都被嵌到一个顶级的 Stack View中了，我们想修改一下 what to see版块的位置，让它位于 weather 版块之后。

从 Outline 视图中选择中间的的 Stack View，然后将它拖到第一、二个 Stack View 之间。
注意：让箭头稍微偏向你正在拖的Stack View左边一点，以便它能够作为外层 Stack View 的 subview 添加。蓝色的小圆圈应当位于两个 Stack View 之间的左端而不是右端：

现在，weather版块是从上到下的第三个版块，由于 Hide 按钮它并不是 Stack View的subview，所以它不会参与移动，它的frame当前是不正确的。

点击 Hide 按钮，选中它：

然后点击自动布局工具栏中的 Resolve Auto Layout Issues 按钮，选择 Update Frames：
现在 Hide 按钮将回到正确的位置：

## 基于配置的 Size 类
最后还有一个任务没有完成。在横屏模式，垂直空间是比较珍贵的，你想将这些版块之间靠得更近一些。要实现这个，你需要判断当垂直Size类为compact时，将顶层 Stack View的 Spacing属性由 20 改成 10.

选择顶层 Stack View，点击 Spacing 前面的+按钮：

选择 Any Width > Compact Height：

在新出现的 wAny hC 一栏中，将 Spacing 设为 10：
编译运行。在竖屏模式下Spacing不会改变。旋转模拟器（⌘←），你会看到各版块之间的间距减少了，现在底部按钮之间的空间也变大了：
如果你没有添加最外层的 Stack View，你仍然可以使用 Size 类将每个版块之间的垂直间距设置为 10，但这就不是仅仅设置一个地方就能够办到的了。

## 动画

现在，在隐藏和显示天气信息时仍然会觉得有一些突兀。你将增加一个动画使这个转换变得更平滑。

Stack View完全支持 `UIView 动画`。也就是说要以动画方式显示/隐藏它所包含的subview，只需要简单地在一个动画块中切换它的 hidden 属性。

让我们来看看代码怎么实现。打开 `SpotInfoViewController.Swift`，找到 
`updateWeatherInfoViews(hideWeatherInfo:animated:)`方法。

将方法的最后一行：
{% codeblock swift lang:swift %}
weatherInfoLabel.hidden = shouldHideWeatherInfo
{% endcodeblock %}
替换为：
{% codeblock swift lang:swift %}
if animated 
{
    UIView.animateWithDuration(0.3) {
        self.weatherInfoLabel.hidden = shouldHideWeatherInfo
    }
} 
else 
{
    weatherInfoLabel.hidden = shouldHideWeatherInfo
}
{% endcodeblock %}

编译运行，点击Hide 按钮或 Show 按钮。是不是加入动画之后看起来要好得多呢？

除了对 Stack View 中的视图以动画的方式设置 hidden 属性，你也可以对 Stack View 自身的属性使用 UIView 动画，例如 Alignment 属性、 Distribution 属性、 Spacing 属性和 Axis 属性。
[开始项目源码](http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/VacationSpots_Starter.zip)
[完整项目源码](http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/VacationSpots_Complete.zip)

## 总结
