+++
title = "Instruments 工具使用"
lastmod = 2020-07-19T21:41:43+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

## 指南 {#指南}

[Xcode调试工具Instruments指南 - CSDN博客](http://100000p.com/article/2c9f60ef66f1c8bb0166f299f42e0030)  

1.  Time Profiler：CPU 分析工具分析代码的执行时间。

2.  Core Animation：离屏渲染，图层混合等 GPU 耗时。

3.  Leaks：内存检测，内存泄漏检测工具。

4.  Energy Log：耗电检测工具。

5.  Network：流量检测工具。


## 学习调试案例 {#学习调试案例}

[如何使用Instruments诊断App（Swift版）：起步 - CocoaChina\_一站式开发者成长社区](http://www.cocoachina.com/articles/12237)  
[How to get Flickr API Key - WP Frank](https://wpfrank.com/how-to-get-flickr-api-key/)  
解决两个问题：  

-   [ ] 笨重问题进入一个详情界面非常慢，另外滑动查询结果的列表也是慢得难以置信--这是一款笨重的 app
-   [ ] 泄漏问题

-   怎样使用 Time Profiler 工具来定位你的代码中的"高消耗点（hot-spot）"，从而让你的代码更加有效率。
-   怎样使用 Allocations 工具来检测和改正代码中的内存管理问题，例如循环强引用。


### 调用树（call tree） {#调用树-call-tree}

1.  Separate by Thread：每个线程被单独考虑。这能让你知道哪一个线程占用 CPU 最多。


## 创建自定义的 Instrument {#创建自定义的-instrument}

[WWDC 2018：创建自定义的 Instrument - 掘金](https://juejin.im/post/5b1cd1025188257d72709d7f)
