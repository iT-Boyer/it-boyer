+++
title = "ox-hugo 常用的导出设置和+OPATIONS:选项"
description = "Test post to test another ox-hugo test."
lastmod = 2021-05-14T13:45:59+08:00
tags = ["hugo"]
categories = ["博客站务"]
draft = false
author = "iTBoyer"
authors = ["iTBoyer"]
+++

## 漫长的经验积累 {#漫长的经验积累}

在工作中，一直在探索如何提高效率，如何把任务合理分解效果，如何坚持知识产出，搭建自己的知识体系。但往往会沉迷在收集工具的使用上，在没有评估该工具到底在实际项目中是否实用。以至于看似每天都非常忙碌，但是工具收集之后，很少真正的实践，产出很少，违背了产能和产出平衡原则。所有的专注力应该集中在真正能改变生活的事情上，耗费了时间和精力反省总结经验，还是无法改变自己的生活，无法改变生活，这都是浪费生命。  

回归正题，在确定学习某项技能时，应该先设定学习目的，要实现什么样的愿景。  

就拿学习 ox-hugo 的过程，虽然经历了一段漫长的积累，但技能的来源全是网上零碎的知识点拼凑的，没有认真考虑过这些知识点的内在联系，它的体系结构什么，导致这块的知识积累都是一盘散沙，再真正使用时，连基础的应用都不知道。现在梳理了之后，有了一些认识，在这里说说:  
ox-hugo 支持 org 导出 md 格式，可以指定导出的目录地址，支持 subtree 导出 md 文件，可以指定导出的 md 格式。toc,num,导出时生成目录，toc 指定是标题的等级，num:标题显示索引.  
目标:工作:目标:认真:学习:  
通过搜集工具（spc X h），新建一篇博客，指定时间和文件名称。指定目录名预览：spc m e H h 导出 md，访问 hugo 服务预览效果。发布到 github  
现在关注的点：导出的目录样式，添加分类和 tags  

总结 ox-hugo 在 emacs 的高级使用  

-   输出格式的设置
-   收集工具的支持
-   section 目录的定义
-   options：可选项的理解
-   org--hugo 属性之间的对应关系


## `STARTUP` {#startup}

**作用**:启动项设置,即打开 org buffer 时,预设显示内容  

```elisp
#+STARTUP: overview
```

**值类型**:数组支持以下属性,指定显示内容的属性  

-   `overview`:只显示一级标题
-   `content`:所有标题
-   `showal`:禁止折叠任何入口
-   `showeverything`:显示抽屉中的内容
-   `indent`:以打开组织缩进模式开,动态虚拟缩进由变量(setq org-startup-indented t)
-   `noindent`:在组织缩进模式关闭的情况下启动


## `hugo_code_fence` {#hugo-code-fence}

切换两种语法高亮的方式  

```elisp
#+hugo_code_fence: nil
```

**值类型**: 布尔  
  `t`: 转为 md 语法高亮块:\`\`\`code\`\`\`  
  `nil`:转为 hugo 高亮语法块:{{}}  
**问题**:出现代码块无法左对齐问题  


## `OPTIONS` {#options}


### `\n` {#n}

**作用**:在转 md 兼容 org 中的换行  

```elisp
#+options: \n:t
```

**org 变量**: `org-export-preserve-breaks`  
**值类型**:布尔  
  `t`:开启换行  
  `nil`:关闭换行  
**问题**:遇到中文字符时无法识别,目前暂时通过设置中文键盘时,输入半圆角字符  


### `^` {#}

**作用**:禁用下标样式转义  

```el
#+OPTIONS: ^:nil
```

**值类型**:枚举  
`^：{}`: `'a_ {b}'` 被转义时，会保持原样 `'a_b'`  
**等价**:(setq-default org-use-sub-superscripts nil)  


### `toc~和~num` {#toc-和-num}

设定导出时,哪些等级的 subtree,可以导出为目录  

```el
#+OPTIONS: toc:t
#+OPTIONS: toc:2 num:2
```

**值类型**: int  
`toc`: 等价(setq org-export-with-toc t)  
`num`: 等价(setq org-export-with-section-numbers 2)  


### `p` {#p}

导出项目计划信息比如  

```el
#+OPTIONS: p:true
```

**值类型**:布尔  
`t`:导出时,保留 schedule time, deadline time 等信息  
`nil`:不导出计划信息  


## 一手资料 {#一手资料}

hugo 文档:[Front-matter | Hexo](https://hexo.io/zh-cn/docs/front-matter.html)  
ox-hugo 文档:[ox-hugo - Org to Hugo exporter](https://ox-hugo.scripter.co/doc/)  
org-mode 文档:[Export Settings (The Org Manual)](https://orgmode.org/manual/Export-Settings.html)  
[导出的设置项](https://blog.csdn.net/railsbug/article/details/107173083)  
[org-mode，最好的文档编辑利器，没有之一 | 心内求法](http://holbrook.github.io/2012/04/12/emacs%5Forgmode%5Feditor.html)
