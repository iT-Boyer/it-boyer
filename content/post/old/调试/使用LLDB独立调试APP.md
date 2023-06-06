---
title: 使用LLDB独立调试APP
date: 2018-09-05 14:03:39
updated: 2018-11-09 20:13:29
comments: true
tags: [调试,lldb]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## 参考
[lldb官方文档](http://lldb.llvm.org)
[苹果文档](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-terminal-workflow-tutorial.html#//apple_ref/doc/uid/TP40012917-CH4-SW2)
[LLDB调试器使用简介 南峰子译](https://southpeak.github.io/2015/01/25/tool-lldb/)
[调试：案例学习](http://www.cocoachina.com/ios/20150330/11435.html)
[系统翻译文章](http://www.jianshu.com/p/09a4c1f7c732)
[Debugging iOS binaries with LLDB](http://codedigging.com/blog/2016-04-27-debugging-ios-binaries-with-lldb/)
[高级LLDB调试](https://www.jianshu.com/p/2d4083d27653)
[LLDB中使用python概览](https://www.jianshu.com/p/381d2d232ec1)
## 善用LLDB
如果想断点到某种场景，不是先随意打个断点然后一直单步到出问题的那行代码，最佳的做法是充分利用LLDB的特性以一次性定位到你所感兴趣的代码。
1. 自定义数据类型
因为LLDB默认是只输出系统类型的，而并不认识自定义的数据类型，所以需要告诉LLDB你所关注的自定义数据类型及其数据，实现对数据的查看。
2. 合理开销
为了避免重新构建所带来的时间开销，你需要学会编写调试代码，以改变应用的执行路径，并修改数据，比如初始化还未初始代的数据。你所输入并执行的表达式会改变应用原本的执行路径，所以对此所带来的副作用你需要有清晰的把控。

### 正确的调试过程
1. 想通过LLDB获取什么信息？
2. 断点到可疑的代码块
3. 在正在执行的代码中一步步执行
4. 观察数据并验证猜想

### LLDB的使用时机和作用
什么时候用LLDB,LLDB能在调试的时候怎样帮助你
1. debug-Only assertion
它能帮助你知道应用中的运行异常，以及各组件之间传递的参数是否与约定的一致。但是不要在assertion中做对应用逻辑有影响的操作，因为一旦构建发布版本，这些assertion都会被屏幕，你在其中执行的操作也就不会进行了。
所以，当发现bug时，LLDB并不是你的第一选择，而应该是debug-Only assertion。
2. log  apple system log(ASL)
而对于在运行中各处都看起来很正常，最终却呈现了错误的结果的情况，log会是一个非常有用的工具。而此处的log指的并不是NSLog，而是apple system log(ASL)，其可以通过console观察到。
* level
ASL可以通过log的level来区分log的严重程序，比如`ASL_LEVEL_EMERG`和`ASL_LEVEL_DEBUG`。
* tag
同时还可以附带使用hash tag以方便log的查找，比如为特定的业务添加特定的业务字符串等。而鉴于log可能被滥用，所以ASL可以通过开关提供某些log是否应该进行。 
正常的开关方式可以是可以使用NSUserDefaults等，或者也可以在shell中设置一个变量，程序在运行的时候读取此值以决定是否进行log。
3. Xcode 静态代码分析器
Xcode可以为程序做的，`-Weverything`和静态代码分析器可以在代码运行前的编译阶段即发现可能存在的问题。可以在之前的session： What's New In LLVM和What's New In the LLVM Compiler中找到相关内容。而在运行期间可以通过Guard Malloc发现堆上缓存overrun的问题，通过zombie objects获取对释放了的对象进行方法调用的问题。具体可以参见Advanced Memory Analysis with Instruments

### LLDB的正确打开方式
1. 两种启模式：一种是通过Xcode上的按钮，另一种是通过console中的lldb调试语言。
2. 三种命令表达式：discoverable,abbreviated及alias形式，
`discoverable`格式: expression --object-description -- foo
`abbreviated`格式:  e -0 -- foo
`alias`格式: po foo
同一个命令三种方式，书写繁琐程度递减，而且可以定义自己的alias 形式的命令。
3. 设置断点
行号断点：`b MyCode.m:4（breakpoint set --file MyCode.m --line 4）`
方法断点：`b "-[MyClass method:]"（breakpoint set --name "-MyClass method:]"）`
selector断点: `b method:（breakpoint set --selector method:）`

### 省时的命令
鉴于在app及Xcode之间因为多次中断而进行的多次切换，可以在断点发生之后执行特定的命令，比如希望查看的数据之后再立即恢复执行
```
b "-[MyClass method:]"
br co a/breakpoint command add
>p rect/expression rect
>bt/thread backtrace
>c/process continue
>DONE
```
这些命令也可以在Xcode面板中通过点击及输入完成同样的功能。
### 条件断点
如果不希望断点频繁触发，可以通过条件断点来达到此目的，比如想在某个特定对象析构的消息处断点，可以这样：
```
p id $myModel = self/expression id $myModel = self
b "-[MyClass dealloc]"/breakpoint set  "-[MyClass dealloc]"
br m -c "self == $myModel"/breakpoint modify --condition "self == $myModel"
```
### 通过watchpoint监控特定内存空间
`watchpoint`的应用场景在于有人会修改某个变量值，你对此很关心但只知道变量的地址，其行为方式是如果变量被访问则watchpoint会暂停app的运行，设置watchpoint的方式是
```
w s v self->_needsSynchronization/watchpoint set variable self->_needsSynchronization
```
受限于CPU的支持程度，在intel平台上，提供了4个slot供watchpoint使用，所以同时只可设置最多4个watchpoint，arm上是2个
watchpoint也可以在Xcode的控制面板中进行操作，只需要在变量区域中右击某变量选择菜单中的watchpoint选项即可
### 避免不断单步的高招
LLDB可以在两种场景下暂停程序的执行：
1. 执行到程序的具体某行代码的时候
这种场景的实用命令是`thread until linenum`，避免一步步单步执行到希望的行，在xcode中这个功能对应的操作是右击代码行选择`continue to this line`。
2. 函数返回之后

### LLDB中手动执行代码
很多时候你可能会发现，要想让你希望执行的代码执行一遍会很困难，比如单元测试的时候某个用例就是无法进行测试，这种情况下你需要用Clang(['kl^n])直接调用这份代码，调用方式是直接在命令行表达式中输入你希望执行的代码并执行，比如
```
b "-[ModelDerived removeDuplicates]"
e -i false -- [self removeDuplicates]/expression --ignore-breakpoints false -- [self removeDuplicates]
```
先在方法上打断点，然后在LLDB中执行此函数，选择不要忽略断点，你会发现执行此`expression`之后会断点到`removeDuplicates`，接着即可对其进行执行。
然而有一点需要特别注意的是，通过LLDB执行的表达式代码是在你的进程中执行的，所以需要对此所带来的后果有自己的认知。
## 检查数据以找寻事情的缘由
在上述的操作之后，我们已经可以断点到我们希望断点的位置了，接着就是检查数据寻找事情发生的原因了，这部分有3方面内容：
### 在LLDB命令行中检查数据
查看局部变量：` frame variable`
执行任意代码： `expression (x+35)` 其会通过app使用的编译器进行编译并在你的app中执行
`p @"hello"` 兼容expression的语法，执行表达式并输出结果
`po @"hello"` 执行任意代码并输出结果的description
### LLDB实用数据格式
需要先搞清楚`raw data`和`data`的区别，
`raw data`:是内存中所存储的数据，但它并不易读，对你来说可能太复杂，或者并不是你理解的数据类型，又或者它的数据量很大。
解析：如果想对raw data有个直观的印象，只需要在Xcode的变量区域选择show raw values就可以在观察任意一个栈帧的时候看到raw data了，而此时切换到show types就可以看到规整而有意义的数据呈现形式了，这就是 LLDB 数据格式所要达到的目的。
对于内置的系统库STL,CoreFoundation,Foundation，其中的数据都已经添加了`Data formatter`，在调试的时候显示都很规整
对于程序员自定义的数据类型的data formatter，苹果构建了可扩展的data formatter子系统，这意味着程序员也可以为自定义的类型添加data formatter。
#### 自定义data formatter
数据类型的data formatter包括两部分：综述summary，用于呈现数据的关键描述；所组成的子数据即synthetic children
以使用python定义summary为例，summary会将一种数据类型与一个python函数映射起来，基础的映射是通过类型名，更多其它规则可以[参见](http://lldb.llvm.org/varformats.html)
这个python函数会在此类型的数据在展示的时候被调用，LLDB会将一个`SBValue`传递给它，SBValue是LLDB对象模型的一部分，可以将其简单地想象成为一个变量，这个python函数最终会返回一个字符串，这个字符串即会被当做summary
#### SBValue
之前提到SBValue可以当做一个变量来对待，可以询问其name,data type,summary(如果有的话),是否有children,有多少children,是否可以详述每个child的信息,每个child的信息其实也是一个SBvalue，所以整个是一个递归的过程。如果值是一个比如数字这样的标量，整数，浮点数等，也可以询问其value。
对自定义类进行summary
```
def MyClass_Summary(value,unused)://其中value是一个SBValue
//由于是自己定义的数据，可先获取其中的成员变量，成员变量也是SBValue
member1 = value.GetChildMemberWithName("_member1")
member2 = value.GetChildMemeberWithName("_member2")
member1Summary = member1.GetSummary()
member2Summary = member2.GetSummary()
#当然也可以做任何你想做的事情，这里仅仅只是简单地组合两个成员的summary
return member1Summary + " " + member2Summary
```
完成了这个python函数之后，变量区域中仍然不能正确显示MyClass的自定义数据类型，因为你还需要在LLDB中执行：
```
ty su a MyClass -F MyClass_Summary/type summary add MyClass --python-function MyClass_Summary
```
#### 审视不透明的数据
先介绍下用于数据分析的expression，可以通过如下形式定义一个持久有效的结构体：
```
expression struct $NotOpaque{int item1;float item2;char* item3;}
```
对于第3方库提供的对象，你可能连其数据类型都不知道，更不会知道其中成员变量的定义，可能通过google之后，可以发现其具体的定义，这时候，就需要使用上述expression再结合summary，即可以在展示的时候使用自定义的data formatter了.
 
 
## 扩展LLDB

### 自定义LLDB命令
通过python脚本，可以为调试器添加新特性，实现自定义的操作/自动化的操作过程
比如计算递归的层数，想想LLDB怎么也算是个强大的程序，数数对它来说应该不是什么难事，更加说相比于你手工一个栈帧一个栈帧地数了。
### LLDB 对象模型
LLDB的强大在于它所使用的LLDB对象模型，我们称其为"SB"(scripting bridge)，这是个`python API`，xcode用其来构建debugger的UI，这意味着对你可以完全地通过LLDB脚本使用SB的所有功能，同时其也有一套对调试session的描述：
对于上述调试界面相信大家都比较熟悉，LLDB对象模型对其的描述是这样：`SBTarget`即是调试中的target，接着在点击了xcode中的运行按钮之后，这个target成为了一个活着的实体，对这个实体，你可以输入，点哪，点哪，点，这即是在机器底层上运行的进程称为SBProcess，进程有着很多用来完成任务的thread，即SBThread，而SBThread会不停地执行function，每个function都会而每次function调用都会对应栈上的一帧，即SBFrame，现在我们已经了解到了描述程序运行中的所有对象，接下来看看怎样完成我们想完成的任务。
首先需要知道的是python命令是如何执行的呢，python函数是与LLDB中的命令一一对应的，LLDB看到这个命令的时候即会调用相应的python命令，python命令的原型是这样:
```
def MyCommand_Impl(debugger,user_input,result,unused):
```
`debugger`:是一个SBDebugger
`user_input`:是用户输入的python字符串
`result`:是SBCommandReturnObject，是用来反馈给LLDB的，反馈执行成功与否等信息
添加自定义的命令的方式如下：
```
co sc a foo -f foo/command script add foo --python-function foo
```
### 断点操作
断点的痛点在于它会不停在中断程序的执行，条件断点会好一点，有了断点action，我们可以只在自己关注的场景停下来
断点action是将断点与一个python函数联系起来，断点命中的时候会调用此python函数，而其可以返回false以勾选断点编辑界面中的continue选择框以让LLDB继续运行
此python函数的原型是
```
def break_on_deep_traversal(frame,bp_loc,unused):
```
`frame`类型:为SBFrame
`bp_loc`类型:为SBBreakpointLocation
绑定python函数的命令是
```
br co a -s p -F foo 1 /breakpoint command add --script python --python-function foo 1
```
首先需要注意的是WWDC中python代码样例的函数function中常看到`unused`作为结尾参数，在实际使用python函数时如果未理解其深意，可能会不知其所以然。
断点中的全局变量`frame`和`bp_loc`分别是断点栈帧的指针及断点处的代码描述信息，`bp_lo`c打印出来类似于这样：
```
2.1: where = YourApp`-[SomeViewController onDynamicMethod:paraList:] + 117 at SomeViewController.m:109, address = 0x000000010f9cf3d5, resolved, hit count = 4
```
在将function作为python命令添加时，命中断点之后lldb会为指定的断点命令函数传入3个参数即`frame`,`bp_loc`和`internal_dict`。

LLDB在设计之初即提供了两种范畴的脚本化方式：
即可以在Unix环境的python应用中使用LLDB开启并进行一段不能进行交互的调试session；
在LLDB中使用python脚本进行诸如监视数据，遍历容器或者决定断点是该暂停程序执行还是继续程序的执行。
#### 在python中操作程序中的变量
在实现这个目标之前，需要解决的一个问题是，程序中的`变量`需要转换成python可以访问的形式，这个时候就需要用到`LLDB API`，它是作为python的LLDB模块提供的。
在LLDB中运行python时，LLDB会自动将当前帧对象通过`lldb.frame`这个python变量提供出来，其类型是SBFrame，可以通过`FindVariable`方法向帧对象询问其本地变量，通过此方法获得的对象是SBValue对象，可以通过SBValue.h中的方法，比如 `GetChildMemberWithName()`, `GetSummary()`, 及`GetValue()`等方法获取具体的信息。
**程序中的变量值最终是如何转换成python中任意取用的信息的呢？**
原来LLDB在捕获到程序作用域中的变量之后都是将其封装在SBValue对象中，通过SBValue的API即可获取到封装在其中的变量值的具体信息，对于对象类型的变量，可以通过`GetChildMemberWithName`获取其成员变量的值，若原本是一个字符串，则使用`GetSummary`获取字符串，通过`GetValue`获取数值相关的值等。
#### 用断点命令script完成最终的临门一脚
有了操作局部变量的神技，实战操作的时候就可以这么玩了，在可疑处打断点，读取临时变量的值检查与预期是否相符，此处的一个小tip是:
将python脚本操作编写在一个python文件里面的function里面，通过import的形式导入并调用其中的function，以免在lldb中输入起来繁琐。
**断点命令script的奥义**
如果在lldb命令行中为某断点添加python脚本，比如通常是这样`breakpoint command add -s python breakpointnum` ，输入一到数行python脚本，则LLDB会自行将这数行代码封装成一个python函数并传递两个参数，即frame和bp_loc，断点命中时，即会调用此函数，并传入断点时当前帧对象`frame`及断点位置信息对象`bp_loc`。
由此可知，在实际编写断点命令的时候，需要注意这样两点：
1. 如果想访问你脚本之外创建的python变量，需要将其声明为`global`，否则其会被当做局部变量，即需要在断点命令脚本中显式声明，比如`global variable`
2. 所有python断点命令脚本都能够访问到frame和bp_loc

举例在断点处的当前栈帧位于特定帧中时命中断点
![](https://upload-images.jianshu.io/upload_images/268750-fd4dea29302d79d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)
第一步通过import导入本地的脚本文件
```
command script import ~/lldb/inGivenFrame.py
```
第二步通过codeline添加断点，
第三步添加断点命令
```
br comm add --script-type python -o "inGivenFrame.inGivenFrame(frame,location,'-[NSObjec givenfuncname]')" 1
```
如果不需要传入第三个参数，则也可以使用`-F`选项，此时命令形式
```
br comm add --script-type python -F inGivenFrame.inGivenFrameF 1
```
此时在断点命中时，lldb传给`inGivenFrameF`函数的第三个参数的意义也有所变更，此时传入的是名为`internal_dict`的变量，包含额外的信息
![](https://upload-images.jianshu.io/upload_images/268750-1d39bf553e26bac3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)
使用`inGivenFrameF`作为断点命令的操作步骤及执行结果如下：
![](https://upload-images.jianshu.io/upload_images/268750-1ef6878f7a3a72ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)









**问题**
现在想通过lldb来调试自己的iOS项目，官方提供了

```
$ lldb /Projects/Sketch/build/Debug/Sketch.app     
(lldb) process launch
```
当按照这种方式指定到路径iOS项目时,打印：

(lldb) target create "/Users/admin/Desktop/Recommend.app"   
Current executable set to '/Users/admin/Desktop/Recommend.app' (arm64).

但是启动时出现问题：

```
(lldb) run     
error: the platform is not currently connected
```
大神，有没有解决办法？
