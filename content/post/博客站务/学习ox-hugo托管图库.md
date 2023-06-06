+++
title = "ox-hugo托管图库，轻量化hugo站库（废弃）"
description = "ox-hugo结合hugo站架构实现资源库托管"
date = 2021-08-09T14:31:00+08:00
publishDate = 2021-08-07T11:19:00+08:00
lastmod = 2021-08-09T23:07:32+08:00
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

## 警告 {#警告}

本文前期思路：借助hugo 和ox-hugo 之间 `static` 目录的特征，以static 作为github托管目录，由于hugo 在编译过程中，会直接将 `static` 目录所有内容，拷贝至 `public` 目录中，包括 `.git` 版本目录。这样就会覆盖 `public` 中的git版本目录。  
[static 目录介绍 - Hugo中文文档](https://www.gohugo.org/doc/themes/creation/#toc%5F4)  

`= 故，得出定论： static 无法用于托管目录，本章的托管方案废弃。`  


## 目的 {#目的}

单纯想把图片资源从hugo库中隔离到单独库中，避免hugo库增大，超出1G空间。  

用到的三个知识点：  

1.  hugo资源库目录结构 `static`
2.  org-download: 支持拖动图片到org中，自动保持到指定目录
3.  ox-hugo :org导出为md过程，将org资源拷贝到 `static` 的机制介绍

{{< figure src="/ox-hugo/static-imgresource.png" caption="&#22270;1&nbsp; 本文涉及到的知识点和问题汇总概览" >}}  

![](http://www.plantuml.com/plantuml/proxy?cache=no&idx=0&fmt=svg&src=https://it-boyer.github.io/iDocs/uml/hugo/static-imgresource.plantuml)  


## ox-hugo导出机制 {#ox-hugo导出机制}

[Image Links — ox-hugo - Org to Hugo exporter](https://ox-hugo.scripter.co/doc/image-links/#references-to-files-outside-the-static-directory)  
对hugo站结构做了相应的支持,ox-hugo在导出资源时，会自动将org文件的文件附件，自动拷贝到 hugo根目录下的static目录中。  

1.  引用~static~目录下的文件例如当文件~foo.png~ 文件在static目录下： `~/hugo/static/images/foo.png`, 在org 种只需要 `/images/foo.png` 。
2.  引用~static~目录之外的文件当file 引用的文件在其他目录时，ox-hugo 定义了相关规则，会自动将文件拷贝到~static~ 目录下，具体规则：[Source path contains _static_](https://ox-hugo.scripter.co/doc/image-links/#source-path-contains-static)
3.  如何禁止ox-hugo 自动拷贝的[Disable auto-copying](https://ox-hugo.scripter.co/doc/image-links/#disable-auto-copying)  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (setq org-hugo-external-file-extensions-allowed-for-copying nil)
    {{< /highlight >}}


## 实现托管的方法 {#实现托管的方法}

-   开启 `ox-hugo` 自动保存功能托管的方案的主要依赖的ox-hugo 的功能， 即 `org-hugo-external-file-extensions-allowed-for-copying` 变量默认值即可。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
      ;;默认值，
      (setq org-hugo-external-file-extensions-allowed-for-copying ("jpg" "jpeg" "tiff" "png" "svg" "gif" "mp4" "pdf" "odt" "doc" "ppt" "xls" "docx" "pptx" "xlsx"))
    {{< /highlight >}}
-   从主库中拆出 `static` ，并托管到github 中，作为图床。主要使用命令： `git subtree split` [Git 仓库拆拆拆 - SegmentFault 思否](https://segmentfault.com/a/1190000002548731)  
    托管到github 之后，在以submodule 子模块方式，集成到主库中。
-   设置 `org-download` 自动保存在 `static` 目录  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
        (setq org-download-method 'directory)
        (setq-default org-download-image-dir "~/hsg/iNotes/static/ox-hugo")
    {{< /highlight >}}

当停用自动拷贝的功能，file 引用的路径就会为相对路径，导致图片资源无法加载。  

如何解决这个问题： 将org-download 的下载目录设置在hugo站资源的~static~ 目录。  

这样通过org-download 添加到org 的图片和ox-hugo 自动保存的目录一致，然后把static 使用subtree 命令，拆出一个新库，专门存放附件资源。  

然后，通过 submodule 的方式从新加入到hugo 中，以git 子模块的方式，来减轻git 库的大小。  

把file 自动引用的路径设置为 hugo目录下的static,  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
(setq-default org-download-image-dir "~/hsg/iNotes/static/ox-hugo")
{{< /highlight >}}

开启自动拷贝功能。这样，file 引用的外链接有 `/static/`, 当ox-hugo自动拷贝资源时，会按照static 规则.
