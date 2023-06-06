+++
title = "plantuml-mode使用问题汇总"
description = "总结plantuml-mode使用过程中遇到的问题，汇总uml在emacs中的常规使用操作"
date = 2021-08-10T19:26:00+08:00
publishDate = 2021-08-10T09:56:00+08:00
lastmod = 2021-08-10T19:26:33+08:00
tags = ["uml"]
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

## 目的 {#目的}

{{< figure src="/ox-hugo/plantuml-mode-xmd.png" >}}  


## plantuml-mode介绍 {#plantuml-mode介绍}

plantuml-mode 支持设置几个属性输出格式：svg png txt  
执行方式：jar server executable  
预览命令：C+c C+c  

输出格式：仅在eamcs-plus版本支持svg  

在emacs-plus上预览，jar模式下，svg/png显示空白，txt 格式错误。server模式：卡死。在emacs上预览：jar/server下，png 乱码，提示type未知。  

executable: 提示 找不到文件时，需要安装plantuml ， brew install plantuml  


## 基于doom中内置配置 {#基于doom中内置配置}


## 自定义私有配置 {#自定义私有配置}

设置org模式下的相关属性  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
(setq org-plantuml-jar-path "~/.emacs.d/.local/etc/plantuml.jar")
(setq org-plantuml-executable-path "/usr/local/bin/plantuml")
(setq org-plantuml-executable-args '("-headless"))
(setq org-plantuml-exec-mode 'jar) ; 'jar  'executable
{{< /highlight >}}

设置plantuml-mode模式下的相关属性  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}

;; Sample jar configuration
(setq plantuml-executable-path "/usr/local/bin/plantuml")
(setq plantuml-jar-path "~/.emacs.d/.local/etc/plantuml.jar")
(setq plantuml-default-exec-mode 'jar) ; 'jar  'executable

(add-to-list 'auto-mode-alist '("\\.plantuml\\'" . plantuml-mode))
(add-to-list 'org-src-lang-modes '("plantuml" . plantuml))

(setq plantuml-server-url "https://www.plantuml.com/plantuml")
{{< /highlight >}}


## 问题 {#问题}

1.  org 中嵌套plantuml时装svg图片时，仅支持 `@startuml` 可以点击超链接。
2.  不支持 `@startmindmap` 脑图和 `@startwbs` wbs图，转svg错误。
3.  `@@md:` 嵌套在org的svg格式，在hugo中显示为图片，无法点击超链接。在hugo中显示，无法点击超链接。  
    
    {{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       @@md:![](http://www.plantuml.com/plantuml/proxy?cache=no&idx=0&fmt=svg&src=https://it-boyer.github.io/iDocs/uml/demo-class.plantuml)@@
    {{< /highlight >}}
4.  `@@html:` 嵌套在org的svg格式，在hugo中显示为图片，无法点击超链接。在hugo站中不显示, 在gitlab上可以显示，无法点击超链接  
    
    {{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       @@html:<img src="http://www.plantuml.com/plantuml/proxy?cache=no&idx=0&fmt=svg&src=https://it-boyer.github.io/iDocs/uml/hugo/static-imgresource.plantuml" alt="sdfds" align="left" title="image title"/>@@
    {{< /highlight >}}
5.  plantuml-mode预览问题  
    1.  预览xx.plantuml文件时，现空白问题
    2.  server模式错误
6.  plantuml-mode语法高亮问题  
     `该方案过期`  
     fork plantuml-mode 参考issue问题，修复高亮问题：  
    [syntax error from examples of plantuml.com · Issue #101 · skuro/plantuml-mode...](https://hub.fastgit.org/skuro/plantuml-mode/issues/101#issuecomment-515623755)  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (defun plantuml-server-encode-url (string)
         "Encode the string STRING into a URL suitable for PlantUML server interactions."
         (let* ((coding-system (or buffer-file-coding-system
                                   "utf8"))
                ;(encoded-string (base64-encode-string (encode-coding-string string coding-system) t)))
                (encoded-string (base64-encode-string  string t)))
           (concat plantuml-server-url "/" plantuml-output-type "/-base64-" encoded-string)))
    {{< /highlight >}}
    
    使用修复的代码：  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (package! plantuml-mode
         :recipe (:host github
                  :repo "it-boyer/plantuml-mode"
                  :branch "develop"))
    {{< /highlight >}}


## 使用 `plantuml` 建模规范 {#使用-plantuml-建模规范}

建模的两个标准，原则，避免选择上的纠结。  

1.  在idocs库中建模，使用在线工具预览uml效果  
    
    通过确定plantuml现有版本问题导致在预览情况下无法正常工作,借助在线预览工具实现预览，然后使用md标记引用到org中。  
    
    在iDocs中创建plantuml源码文件，在线工具校验预览，使用 `@@md` 嵌套在org中。
2.  在org中建模  
    
    结构简单的模型，在org中建模，以png格式为主。切记脑图/wbs图不支持svg格式。
