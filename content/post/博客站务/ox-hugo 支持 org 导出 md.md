+++
title = "在 hugo 站点实时预览 org 生成的 md 效果"
description = "Test post to test another ox-hugo test."
date = 2021-05-08T19:32:00+08:00
lastmod = 2021-05-10T11:42:33+08:00
tags = ["hugo"]
categories = ["博客站务"]
draft = false
author = "iTBoyer"
authors = ["iTBoyer", "iTBoyer2"]
+++

## 目的 {#目的}

要想实现在 hugo 站点实时预览 org 生成的 md 效果,需要借助 ox-hugo 提供的自动导出的功能,配合 hugo server 实时刷新的特性,达到实时预览的目的.  

1.  **实时预览**:hugo server 是 hugo 常用的命令,只要启动了站点服务,就可以实时预览 md 的变更.
2.  **自动导出**:ox-hugo 是基于 **实时预览**,为 <span class="underline">emacs</span> 用户提供了一个强大的 **自动导出** md 的功能.  
    即:当每次编写完成保存 org 文件时,会根据 Export 相关属性,自动导出 md 文件到 hugo 站点,从而实现在 hugo 实时预览 md 效果.

ox-hugo 的自动导出功能有类似作用域的概念,可以针对目录下所有文件,也可以仅在当前 org 文件中起作用,也可以指定 org 文件不支持自动保存.  

> 您可以按照以下步骤进行操作，这些步骤适用于 org 文件和 subtree 节点：  
[Auto-export on Saving — ox-hugo - Org to Hugo exporter](https://ox-hugo.scripter.co/doc/auto-export-on-saving/)  

下面仅对自动导出功能的配置说明.  


## 作用在当前 org 文件 {#作用在当前-org-文件}

当仅想在一个文件中自动保存,可以在 org 文件底部添加如下:  

```elisp
* Footnotes
* COMMENT Local Variables :ARCHIVE:
# Local Variables:
# eval: (org-hugo-auto-export-mode)
# End:
```


## 作用在目录下所有 org 文件 {#作用在目录下所有-org-文件}

举例,当想在 `~/your/org` 目录下:  

```shell
vi ~/your/org/.dir-locals.el
```

在文件中添加如下内容  

```elisp
 ((org-mode . ((eval . (org-hugo-auto-export-mode)))))
```

保存之后,在 emacs 编辑 org 并保存,验证是否成功.  


### 屏蔽该目录下 org 自动保存 {#屏蔽该目录下-org-自动保存}

```elisp
​* Footnotes
​* COMMENT Local Variables                          :ARCHIVE:
# Local Variables:
# eval: (org-hugo-auto-export-mode -1)
# End:
```
