---
title: Instrument使用Signposts工具
date: 2018-10-02 23:19:55
updated: 2018-10-15 15:21:53
comments: true
tags: [iOS,调试]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---

## 引言

性能是实现卓越的用户体验的关键之一。当应用或者游戏表现的运行迅速，反应灵敏时，用户会更喜欢。但是软件是很复杂的，当你的应用视图做某事时，例如只是点了一个按钮，但程序也有可能做了很多的事情，这就意味着你可以在一些看似不太可能的地方找到一些优化点。但这样做，挖掘性能的优化点，有时就需要深入理解你的程序正在做些什么。它需要您知道代码什么时候执行的，以及特定的操作需要多长时间。所以这就体验出来了有一个好的测试工具是多么的重要。

我们知道开发更好的工具，并让开发者使用这些工具，是我们帮助您成为更高效的开发人员的方法之一。所以今天我们要谈谈其中的一个工具——`Signposts`（路标）。

## 介绍Signposts及历史

`Signposts`是`OSLog`家族的新成员，我们正准备让它支持`iOS`和`macOs`。您可以在swift和C中使用它们，但最酷的是我们把它和`Instruments`集成在了一起。这就意味着`Instruments`可以获取`Signposts`所产生的数据，并且让深入你理解你的程序正在做什么。

首先要介绍一点历史，几年前我们介绍了`OSLog`。这是我们现代化的呈现日志记录的工具。这是我们从系统中获取调试信息的方法。它是在我们“效率”、“隐私”的目标下完成的。具体了解，请查看[WWDC 2016 Unified Logging and Activity Tracing](https://developer.apple.com/videos/play/wwdc2016/721/)。

这里你可以看到一个`OSLog`的例子，创建一个简单的日志句柄，并且像它发送了一个Hello world。
```
let logHandle = OSLog(subsystem: "com.example.widget", category: "Setup")
os_log(.info, log: logHandle, "Hello, %{public}s!", world)
```
`Signposts`扩展了`OSLogAPI`，但它们是为性能用例而做的。这意味着它们正在传达与性能相关的信息，并且它们与我们的开发工具集成在一起，您可以使用`Signposts`对代码进行标注，然后启动`Instruments`查看类似的内容。因此，`Instruments`向您展示了您的程序正在做的事情的可视化时间轴，以及`Signposts`展示在上面。然后底部有个表，统计汇总和分析`Signposts`数据，数据分成一块一块的，可以让您看到程序到底做了些什么。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/1.jpg)
在文章中，我将谈谈如何在您的代码中使用`Signposts`，以及展示一下他们的作用。然后展示`Instruments`中`Signposts`的可视化界面，让您了解`Instruments`和`Signpost`是如何协同工作的。

## 使用Signposts
1. `Siginposts`的两个函数:`os_signpost(.begin, ...)`和`os_signpost(.end, ...)`.
2. 日志句柄:`日志句柄`需要两个参数一个是`子系统`，一个是`类别`.
让我们从一个非常基础的例子开始。想象一下这是你的应用。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/2.jpg)
你要研究的就是一个接口特定部分刷新所需要的时间。你知道你需要加载一些图片并在屏幕上展示。所以，在这个简单、抽象的视图中，可能你需要做的就是获取图片资源。
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/3.jpg)
所有操作完成后，界面会刷新。`Signposts`允许我们在一系列工作的`开始`和`结尾`进行标记，然后将这两个时间点关联起来，同时这两个日志事件也会互相关联。这就需要调用`Siginposts`的两个函数。`os_signpost(.begin, ...)`和`os_signpost(.end, ...)`。图中`b`箭头代表开始，`e`箭头代表结束。然后我们将这两个时间点互相关联，让您了解这一段经过的时间。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/4.jpg)
在代码中，有一个简单的算法实现，对于我们接口中的每个元素，我们将获取该资源，这正是我们想要去测量的。
```swift
for element in panel.elements {
    fetchAsset(for: element)
}
```
因此，为了将`Signposts`代码加入到基本的代码中，我们需要导入`import os`模块，然后由于`Siginposts`是`OSLog`的一部分，所以我们需要创建一个`日志句柄`。这个`日志句柄`需要两个参数一个是`子系统`，一个是`类别`。
* 子系统: 在整个项目中可能都是相同的，它看起来很像您的app的包名。它代表了组件或者软件，或者是您正在使用的框架。
* 类别: 用于关联，将相关的操作或者`Signposts`绑定在一起。之后我们会讲到这么做的好处。

当我们拥有了日志句柄，我们只需要调用`Siginposts`的`.begin`和`.end`两个函数即可。参数中，我们将日志句柄传递过去，然后再传递一个`Signpost`的名字。名字是一个字符串，用来标识我们感兴趣的操作的时间间隔。
```swift
import os.signpost

let refreshLog = OSLog(subsystem: "com.example.your-app", category: "RefreshOperations")

for element in panel.elements {
    os_signpost(.begin, log: refreshLog, name: "Fetch Asset")
    fetchAsset(for: element)
    os_signpost(.end, log: refreshLog, name: "Fetch Asset")
}
```
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/5.jpg)
所以返回之前的时间轴，它就变成了这个样子。在每次开始、结束获取资源时，我们都添加了一个`路标`。因为在开始和结束的路标的标识一样，所以我们可以将他们两者匹配在一起。但是，如果我们还想要测试整个操作的全部时间，整个刷新过程是怎样的，该如何去做呢？我们在代码中，只需要再添加一对新路标即可（新的名字）。
```swift
import os.signpost

let refreshLog = OSLog(subsystem: "com.example.your-app", category: "RefreshOperations")

os_signpost(.begin, log: refreshLog, name: "Refresh Panel")
for element in panel.elements {
    os_signpost(.begin, log: refreshLog, name: "Fetch Asset")
    fetchAsset(for: element)
    os_signpost(.end, log: refreshLog, name: "Fetch Asset")
}
os_signpost(.end, log: refreshLog, name: "Refresh Panel")
```
这时，我们的时间轴又发生了变化。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/6.jpg)
### 异步用例：测量异步任务的时间
1. `Signpost IDs`路标ID:区分重叠时间间隔的方法，将告诉系统哪些是相同类型的操作，但每个操作彼此不同。
2. 
上面是一个很简单的例子。如果你的应用顺序执行第一步、第二步、第三步等等，那么这样测量是非常有效的。但是我们会经常用到一些异步的工作，他们可能同时发生，他们之间也可能会有重叠或者交叉。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/7.jpg)
在这种情况下，我们需要向系统提供一些额外的信息，以便系统可以将这些路标彼此区分开来。到现在为止，我们在调用方法的时候只用到了名字，通过名字将相同路标的绑定在一起。名字已经确定了时间间隔，但是没有给我们一种区分重叠时间间隔的方法，所以在这里引入了`Signpost IDs`。路标ID将告诉系统哪些是相同类型的操作，但每个操作彼此不同。在路标开始和结尾传递相同的id，则系统知道这两个是关联的。你可以通过日志句柄来得到一个路标ID。
```swift
let spid = OSSignpostID(log: refreshLog)
os_signpost(.begin, log: refreshLog, name: "Fetch Asset", signpostID: spid)
//  some code or even async...
os_signpost(.end, log: refreshLog, name: "Fetch Asset", signpostID: spid)
```
同样你也可以通过一个对象来得到`日志句柄`。如果你有一些对象代表您正在尝试的工作，只要您使用该对象实例就会生成相同的路标ID。这就意味着您不用去存放路标ID，只需要通过对象去管理ID就好了。
```
let spid = OSSignpostID(log: refreshLog, object: element)
```
在视觉上，您可以将路标ID视为允许我们向每一个路标调用传递一些额外的上下文，这可以将特定操作的开始和结束标记互相关联。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/8.jpg)
这很重要，因为这些操作不仅仅可以重叠，而且通常他们需要的时间也不同。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/9.jpg)
我们现在把示例中的`fetchAsset`从同步调用改为异步调用。并且由于是异步的，这些时间间隔可能会相互重叠，所以我们还要在创建路标时加上路标ID。
```swift
let refreshLog = OSLog(subsystem: "com.example.your-app", category: "RefreshOperations")

let spidForRefresh = OSSignpostID(log: refreshLog)
os_signpost(.begin, log: refreshLog, name: "Refresh Panel", signpostID: spidForRefresh)

for element in panel.elements {
    //通过对象去创建路标ID
    let spid = OSSignpostID(log: refreshLog, object: element)
    //通过ID去记录一个路标
    os_signpost(.begin, log: refreshLog, name: "Fetch Asset", signpostID:spid)    
    fetchAssetAsync(for: element) {
        //每一个完成之后的回调
        os_signpost(.end, log: refreshLog, name: "Fetch Asset", signpostID: spid)
    } 
}
//  全部完成完成的回调
notifyWhenDone {
    os_signpost(.end, log: refreshLog, name: "Refresh Panel", signpostID: spidForRefresh)
}
```
这样就完成了，您可以将`Signposts`视为一种分类或等级制度。所有的操作都通过日志句柄相关联，这意味着日志分类。然后对我们感兴趣的每个操作，我们给它一个路标名字，如果可能有重叠，我们在给他们`路标ID`，告诉系统虽然名字相同了，但是我希望通过ID区分。

这个接口设计的特别灵活，所以你可以控制起始点和结束点的所有参数，日志句柄，路标名字及ID。只要传递的参数是一致的，起始点与结束点就可以对应上，即便他们写在了不同的方法或者文件中。我们之所以这样做是因为希望您能够将它应用到实际开发中。

### 在Signposts中添加元数据
您可能希望在路标中传达一些额外的信息，额外的性能相关的信息。很巧，我们有一个方法来为路标添加元数据。下面是一个基本的路标。
```swift
os_signpost(.begin, log: log, name: "Compute Physics")
```
我们可以添加一个额外的`字符串`（OSLog格式化的）参数。这允许您向开始点与结束点添加一些上下文。我们可以通过`%d`来传递`整数`。当然也可以传递`其他的格式化类型`的参数。
```swift
os_signpost(.begin, log: log, name: "Compute Physics", "%d %d %d %d",x1, y1, x2, y2)
os_signpost(.begin, log: log, name: "Compute Physics","%{public}s %.1f %.1f %.2f %.1f %.1f", description, x1, y1, m, x2, y2)
```
至于字符串的长度，不用担心，您可以自由、随意的使用。该字符串也会全部渲染到Instruments的界面中，或者仍然可以在程序中访问附加的数据。
### 添加独立事件
除了元数据之外，您可能还希望及时添加单独的时间点。这就表示，除了开始的路标以及结尾的路标外，你可能还有一个路标，该路标没有连接到特定的时间间隔，而是一些固定的时刻。为此，我们提供了一个带有事件类型的路标。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/10.jpg)
用法与设置开始、结束的路标类似，只不过它标识一个单一的时间点。
```swift
os_signpost(.event, log: log, name: "Fetch Asset", "Fetched first chunk, size %u", size)
os_signpost(.event, log: log, name: "Swipe", "For action 0x%x", actionCode)
```
您可以在间隔的上下文中使用该方法，或者一些您想追踪的与用户交互无关等时间间隔无关的内容。如果您真的在调查一个性能上的问题，您可能会大量使用它。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/11.jpg)
## 有条件启动Signposts
1. 路标通常默认是开启
如果你启用了一些路标，那么他们通常默认是开启的，但我想谈谈有条件的打开和关闭它们。首先我要强调一下，路标是轻量级的。这说明我们已经做了很多优化。我们通过编译器优化来完成这些工作，这些优化在编译时就做了，而不是在运行时完成的。我们还推迟了很多工作，以便他们在Instruments后端完成。这意味着路标应该占用很少的系统资源。我们之所有这样做是因为我们希望尽量减少对您代码的影响。我们也做到了这一点，因为我们确保即使您的时间跨度非常小，也可以发出许多路标来获取一些细粒度的测量结果。
1. 关闭路标
但您可能希望能够关闭路标。要做到一这点，我们将利用`OSLog`的功能，即禁用的`日志句柄`。禁用的`日志句柄`也是一个简单的句柄。它的作用是针对该句柄进行的每个`OSLog`和`os_signpost`调用都会几乎会变为无操作。事实上，如果你在C中采用这个，我们甚至会对你进行检查，然后我们甚至不会评估其余的参数。所以你可以在运行时更改这个句柄。

举个例子，让我们回到第一个示例的代码上。我以一个环境变量作为条件，来初始化。如果包含该变量，那么使用普通的`os日志构造函数`；如果不包含，那么将使用禁用的`日志句柄`。
```swift
let refreshLog: OSLog
if ProcessInfo.processInfo.environment.keys.contains("SIGNPOSTS_FOR_REFRESH") {
    refreshLog = OSLog(subsystem: "com.example.your-app", category: "RefreshOperations")
} else {
    refreshLog = .disabled
}
os_signpost(.begin, log: refreshLog, name: "Refresh Panel")
for element in panel.elements {
    os_signpost(.begin, log: refreshLog, name: "Fetch Asset")
    fetchAsset(for: element)
    os_signpost(.end, log: refreshLog, name: "Fetch Asset")
}
os_signpost(.end, log: refreshLog, name: "Refresh Panel")
```
`环境变量`是您在调试程序时可以在`Xcode scheme`中设置的内容。现在我说你不必在调用中进行更改，但这种方式相当昂贵并且只适用于调试时。因此如果您有一些基于`Instruments`的特定的功能，你可以检查特定的`日志句柄`，查看它是否打开了`siginposts enabled`属性。然后试用该属性来控制添加该附加操作。
```swift
if refreshLog.signpostsEnabled {
    let information = copyDescription() os_signpost(..., information)
}
```
## C语言中的Signposts
上面我们所有示例都是swift的，但是C语言中也提供了路标。到目前为止，上述功能都是可用的：长句柄、使用不用的路标、以及管理路标标识符。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/12.jpg)
那些对C语言中使用路标感兴趣的人，推荐你们阅读头文件中的文档。文档中包含了所有信息，都是从C语言开发人员的角度考虑的。
## Instruments中使用路标
现在介绍完如何在代码中应用`路标`，接下来很开心为大家介绍`路标`和`Instruments`是如何在一起工作的。向大家展示`Instruments 10`中三个重要的新功能，来帮助您使用路标数据。
* 路标工具:该工具允许您记录、查看和分析应用程序中所有的路标活动。
* 兴趣点:谈谈什么是兴趣点，以及何时您要设置一个兴趣点
* 自定义工具: 介绍新的自定义工具，以及如何将它与路标一起使用，以获得更精致的路标展示。
### 路标
接下来就看下例子吧。示例的app名叫开拓者，主要是展示一些风景。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/13.jpg)
当我们滚动时，最初展示一个白色的背景，当图片下载完之后，会填充到白色背景区域。这是一种常见的设计，尽管这样设计可以提升一些用户体验，但很难分析它的性能，因为在这个过程中进行了很多异步的活动。如果用户快速滚动，那么有可能出现某个单元格还没有下载完毕之前，就已经要被重用了，那么就必须取消下载。如果不取消的话，可能会有几个并行下载，而展示出来的图也不一定是我们想要的（图和标题不对应）。接下来看看如何通过路标来分析这个应用吧。 
首先，在每个单元格中，有一个`startImageDownload`方法。当我们需要下载图片时会调用它，并传递要下载图片的名字。方法内部，有一个图片下载类，我们通过图片名字和设置自己为委托来初始化该类。在这种情况下，由于`downloader`对象代表正在进行的并发活动，因为下载是异步的，所以可以通过`downloader`来创建一个`路标ID`。接着在下载开始之前，调用`os_signpost(.begin，...)`方法，设置`日志句柄`、`名称`、`ID`、以及一些元数据。然后开始下载图片，并且将`downloader`设置为当前单元格正在运行的下载器。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/14.jpg)
图片下载完毕的回调中将图片展示到屏幕上，然后调用`os_siginpost(.end, ...`方法，设置`日志句柄`、`名称`、`ID`、以及一些元数据。您会注意到，我们用`xcode:size-in-bytes`注释了这个参数。它的作用是告诉`Xcode`和`Instruments`这个参数应该被视为展示和分析字节大小。这种被称作为`工程类型`。可以`Instruments`开发人员帮助文档中找到他们的详细介绍。最后，我们下载完毕了，将下载器设置为空。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/15.jpg)
当然下载除了成功以外，还有失败的情况，在单元格准备重用时，进行相关的路标设置。
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/16.jpg)
设置完之后，就可以来进行分析了。打开Instruments后，可以选择一个空白的文档。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/17.jpg)
然后从右侧添加中选择os_signpost工具，并推动到轨迹中。
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/18.jpg)
接下来就可以在app中操作了，一阵操作猛如虎之后，我们回到`Instruments`来看一下。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/20.jpg)
通过选择某一段时间，可以查看这段时间内的情况。我们可以看到左侧的路标名称`Background Image`，以及右侧的一些使用元数据来注释的时间段。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/19.jpg)
现在再缩小然后看看其他未知的追踪，我们注意到我们最多只有五个并行下载图片的任务，这是一件好事，证明我们取消了。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/21.jpg)
查看底部间隔的摘要，我们看到是按照类别、路标的名字、开始的消息和结束的消息来区分的。我们来看一共触发了93次的图片下载请求。其中location1触发了12次，7次取消，5次下载成功，下载成功时间共计3.04秒，3.31MB。并且可以查看每次请求时间的最小值、最大值和平均值。如果对某类数据感兴趣，可以点击进入查看详细内容。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/22.jpg)
如果要查看元数据相关的内容，可以进行筛选，然后查看数据。 
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/23.jpg)
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/24.jpg)
这是一种很好的方式来查看您通过元数据传达的值的统计分析.
### 兴趣点
接下里，我们看看`兴趣点`。回到应用，我们我们在主页点击一个单元格，就会进入详情页面。  
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/26.jpg)
如果现在我们每次都能追踪这些详情页面出现的时间就太好了，因为这样我们就可以知道用户正在做什么，并且我们知道用户在app中的哪个页面。当然可以通过路标完成这件事，但是你需要在`Instruments`中把它拖到轨迹中，并且记录所有的活动。这样有点淡化了导航事件的重要性。所以我们提供了兴趣点。

点在我们在详情页面的代码中查看`viewDidAppear`方法，我们通过`os_signpost(.event, ...`来创建一个`路标事件`。这个事件要发送到我们创建的称之为`兴趣点`的`日志句柄`中。`类别`设置为`兴趣点`，这正是`Instruments`寻找的一个特殊类别。
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/27.jpg)
我们回到再次打开`Instruments`选择时间分析，会发现自动有了兴趣点这一项。
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/28.jpg)
然后开始录制，从首页进入详情页，选择不同的单元格反复执行，然后返回Instruments，就可以看到这些兴趣点了。
![](https://raw.githubusercontent.com/kingsword/BlogSnapShot/master/WWDC%202018%20sessions/405/29.jpg)
因此您可以在看到用户在哪个页面，并且将其与其他性能数据相关联。
## 自定义Instruments
视频中，具体没有讲如何创建，如果想了解如何创建的话，可以参考一下[WWDC 2018 Creating Custom Instruments](https://developer.apple.com/wwdc18/410)。
[WWDC 2018 session 405](https://developer.apple.com/videos/play/wwdc2018/405/)
转自[使用日志记录来衡量性能](https://blog.csdn.net/tugele/article/details/81252603)


