+++
title = "学习elgantt.el"
description = "甘特图的使用和注意事项，监督任务提高效率"
date = 2021-09-06T17:44:00+08:00
publishDate = 2021-09-06T16:46:00+08:00
lastmod = 2021-09-08T14:31:43+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [目的](#目的)
- [使用](#使用)
- [问题](#问题)

</div>
<!--endtoc-->


## 目的 {#目的}

在学习taskjuggler 工具过程中，碰巧一起学习emacs 中绘制甘特图的工具elgantt.  

在taskjuggler 实战中积累了一些技巧经验，认识到taskjuggler 基础用法和功能。它通过导出org 文件中的属性信息，创建网页版预览任务进度。整体预览效果比较好。  

回顾下tj3主要相关的字段. 项目基础信息，设置起始时间，设置每天工作时长，指定工作时间段，启动基准功能。  

1.  任务信息：project 标签指定导出的项目，依赖项，优先级，评估量，持续时间
2.  资源模块：分配资源
3.  里程碑：设置依赖

具体信息可以参考：使用tj3 制定周计划培养七习知识体系  


## 使用 {#使用}

总之，甘特图三要素：任务，时间（开始，结束，里程碑），资源（人、机器、资金）。在三个素材之间找到平衡点，合理分配任务，规划资源就好。  

当看到elgantt 工具，是基于emacs 中绘制甘特图的功能，有灵活的自定义方式，能够很好的呈现甘特图的相关信息。  

现在说下，从README.md 中了解的几个功能点：  

1.  安装和相关设置  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (package! elgantt
         :recipe (:host github :repo "legalnonsense/elgantt"))
    
       (package! org-ql
         :recipe (:host github :repo "alphapapa/org-ql"))
    {{< /highlight >}}

2.  扩展elgantt 支持属性

<!--listend-->

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
;;========扩展elgantt的属性
(elgantt-create-display-rule add-org-properties-display
  :parser (
           (elgantt-priority . ((org-entry-get (point) "PRIORITY")))
           (elgantt-effort . ((org-entry-get (point) "Effort")))
           (elgantt-blocker . ((org-entry-get (point) "BLOCKER")))
           (elgantt-TOTAL_PAGES . ((org-entry-get (point) "TOTAL_PAGES")))
           (elgantt-PAGES_READ . ((org-entry-get (point) "PAGES_READ")))
           )
  :body (())) ;; insert code here, which can use elgantt-priority variable
{{< /highlight >}}

1.  设置展示内容的起始日期设置展示的最早的时间点，相对tj3不支持设置项目工期。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
           ; 设置展示内容的起始日期
           (setq elgantt-start-date "2020-01-01")
    {{< /highlight >}}
    
    可以设定预览时，直接调到显示当前月份位置。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
           elgantt-scroll-to-current-month-at-startup t
    {{< /highlight >}}
2.  连线：根据相同特性，通过连线，显示之间的关系 hashtag 对应org 中的tag 必须添加井号 如:#tag:
3.  绘制阅读进度条
4.  绘制进度

相比taskjuggler 功能简单，能满足查看任务简单的时间线，查看任务之间的关系，可以根据关注内容，定制不同的显示样式：outline,hashtag 等。  

支持加载多个org 文件，可以快速定位到任务位置。  

可以扩展字段，根据字段绘制进度条等样式。  

看了两天的readme, 一点点跟着操作，可以很快的掌握相关技巧。  

接下来，就是专注使用tj3 ，制定计划，规划任务进度。先熟练了在项目管理的概念，摸索实际中能快速实践的方法，然后，在扩展elgantt，目前的使用场景，当作日历，查看进度条，具体任务规划，严格按照tj3 来实现。  


## 问题 {#问题}

当出现中文时，elgantt 视图无法对齐。  

hashtag ：添加tag 时，必须井号（#）开始。 :#tag:
