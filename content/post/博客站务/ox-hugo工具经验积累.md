+++
title = "ox-hugo在博客写作过程积累总结"
description = "Test post to test another ox-hugo test."
date = 2021-07-16T12:43:00+08:00
publishDate = 2021-07-16T00:00:00+08:00
lastmod = 2021-07-16T14:28:08+08:00
tags = ["hugo"]
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [<span class="org-todo done DONE">DONE</span> 写作目的](#写作目的)
- [导出时todo 状态隐藏的问题](#导出时todo-状态隐藏的问题)
- [隐藏todo 状态转换之后的时间戳的方法](#隐藏todo-状态转换之后的时间戳的方法)
- [自定义todo 关键字的样式](#自定义todo-关键字的样式)
- [温习了几个标签的使用](#温习了几个标签的使用)
- [意外收获](#意外收获)

</div>
<!--endtoc-->


## <span class="org-todo done DONE">DONE</span> 写作目的 {#写作目的}

开这篇文章的目的是，总结今天在导出博客过程中遇到的问题和解决方式和办法。也希望在以后使用ox-hugo过程中，在遇到类似问题可以统一在这里汇总，在这里找到解决问题的思路和办法。  

今天遇到的几个问题：  

1.  导出时todo 状态隐藏的问题
2.  隐藏todo 状态转换之后的时间戳的方法
3.  自定义todo 关键字的样式
4.  温习了几个标签的使用
5.  意外收获

解题思路是走官方提供的一套demo，在demo 检索相关操作和实现，收获了几个方法  

检索上：在目录中，搜索关键，检索当前目录下的所有文件  ： spc s d  


## 导出时todo 状态隐藏的问题 {#导出时todo-状态隐藏的问题}

想要使用ox-hugo 规范化写作流程，就需要通过agenda管理跟踪日常的写作。这就必须面对进度状态关键字和ox-hugo 导出过程的兼容问题。  

今天针对这个问题，查看了官方demo 给的例子， `todo` 选项控制在导出时，隐藏或显示todo关键字。  

1.  导出时，显示：当在写作阶段，在网页段显示todo状态，可以方便跟踪任务状态和进度，把todo 设置为t即可。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       :PROPERTIES:
       :EXPORT_OPTIONS: todo:t
       :END:
    {{< /highlight >}}
2.  导出时，隐藏：在完成写作校对之后，导出成品时，可以通过设置todo 为 nil 隐藏todo 关键字。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       :PROPERTIES:
       :EXPORT_OPTIONS: todo:nil
       :END:
    {{< /highlight >}}


## 隐藏todo 状态转换之后的时间戳的方法 {#隐藏todo-状态转换之后的时间戳的方法}

在切换任务状态时，会有活动状态变更log记录，在ox-hugo导出时，这些记录显示会显示网页中：  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
- State "DONE"       from "TODO"       [2021-07-15 Thu 19:22]
{{< /highlight >}}

如何即保留这些记录，导出时，ox-hugo又可以忽略这些记录，在看了官方demo发现针对该问题的属性： `:LOG_INTO_DRAWER: t`  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
:PROPERTIES:
:LOG_INTO_DRAWER: t
:end:
{{< /highlight >}}

当该属性开启状态时，日志信息会自动添加到 `LOGBOOK` 中，此时在执行导出，状态记录会被自动忽略。  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
:LOGBOOK:
- State "DONE"       from "TODO"       [2021-07-15 Thu 19:22]
:END:
{{< /highlight >}}


## 自定义todo 关键字的样式 {#自定义todo-关键字的样式}

这个功能是在学习隐藏显示todo 关键字过程中的意外发现，目前测试还没有生效，先贴上代码，以后在做研究：  

具体方法：  

1.  先声明todo 的css样式  
    
    在org 文件声明方式，即新建一个节点，例如 `CSS for TODO`, 并设置不支持导出 `:noexport:`,如下：  
    
    {{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       CSS for TODO :noexport:
       :PROPERTIES:
       :CUSTOM_ID: todo-css
       :END:
       #+begin_export html
       <style>
       .org-todo {
           font-size: 0.8em;
           font-weight: 700;
       }
       /* *** Org TODO set to TODO state */
       .org-todo.todo {
           color: #e60000;
       }
       /* *** Org TODO set to DONE state */
       .org-todo.done {
           color: green;
       }
       </style>
       #+end_export
    {{< /highlight >}}
2.  在节点处引用自定义的css样式  
    
    上述样例中，最关键是提供一个id `:CUSTOM_ID: todo-css`, 让后就可以引入：  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       #+include: "./all-posts.org::#todo-css" :only-contents t
    {{< /highlight >}}


## 温习了几个标签的使用 {#温习了几个标签的使用}

在导出过程中，有很多重要的属性，经常忘记，今天重新熟悉了几个，作为巩固学习。  

1.  toc ： 是否生成索引目录， 可以指定索引的深度，例如： `toc:2` 生成两级以内的索引，配合使用还有 `num`, 例如： `num:2` 给索引添加索引号码，支持两级内。
2.  h:3 ：支持导出的等级数。


## 意外收获 {#意外收获}

1.  目录下检索关键字在目录中，搜索关键，检索当前目录下的所有文件  ： spc s d  
    
    需要安装第三方工具：ripgrep `brew install ripgrep`

2.  TODO 转为DONE ，自动添加Colsed  
    (setq org-log-done 'time)  
    或  
    
    [Closing items (The Org Manual)](https://orgmode.org/manual/Closing-items.html)
