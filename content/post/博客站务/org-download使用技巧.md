+++
title = "org-mode插入图片的方法"
description = "在org-mode中插入图片"
date = 2021-08-09T14:31:00+08:00
publishDate = 2021-08-05T17:26:00+08:00
lastmod = 2021-08-09T14:31:24+08:00
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

## <span class="section-num">1</span> 目的 {#目的}

使用图床方式托管图片，减小hugo源文件对github库的占用控件。  

在使用markdown时，可以使用 `！[](imgUrl)` 语法加载图床中的图片。  

现在想在org-mode中实现同样的效果，尝试了以下几种方式，都无法实现。  
org-mode插图仅支持文件模式:  
>具体插入流程：org-download本身会自动在org文档当前目录下创建一个与文档同名的文件夹来保存图片，然后支持多种途径的图片插入，插入之后会复制或者下载一张图片到图片文件夹下面。  

参考的资料：  
[Images in Content — ox-hugo - Org to Hugo exporter](https://ox-hugo.scripter.co/doc/images-in-content/)  
[利其器─Emacs/orgmode插入图片 - 知行合一](https://vinurs.me/post/d4070283-b585-48b4-9275-99721b1ee36d/)  
[使用Emacs中的org-mode写cnblogs之图片插入 - yangwen0228 - 博客园](https://www.cnblogs.com/yangwen0228/p/6287455.html)  


## <span class="section-num">2</span> 探索插入图片的几种方案 {#探索插入图片的几种方案}

1.  外连接图片直接粘贴路径到org中即可。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       #+ATTR_HTML: :width 50% :align center
       https://devalot.com/assets/articles/2008/07/project-planning/expanded.jpg
    {{< /highlight >}}
    
    导出md后的格式：  
    
    {{< highlight html "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       {{< figure src="https://devalot.com/assets/articles/2008/07/project-planning/expanded.jpg" width="50%" >}}
    {{< /highlight >}}
2.  使用脚本生成图片  
    snippet命令：uml/xmd/wbs  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       #+BEGIN_SRC plantuml :file ../iPics/images/emacs.png
       @startmindmap
       caption 总结emacs 知识体系
       title emacs 知识体系
       center header
               通过结构来描述emacs 最受用的工具
       endheader
       * root node
       ** child node left side
       ** child left node
       ***_ child2 left node
       '给图表添加备注
       legend right
               完成相关技能的总结回顾
       endlegend
    
       center footer
               结束
       endfooter
       @endmindmap
       \#+END_SRC
    {{< /highlight >}}
3.  img直接导出markdown语法  
    snippet命令：umlimg  
    
    {{< highlight markdown "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       @@md:![](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://it-boyer.github.io/iDocs/uml/UGM-gtd.plantuml)@@
    {{< /highlight >}}
    
    markdown语法必须使用反转义包围： `@@md: md语法 @@` 否则，导出的会添加反斜杠转义符: `\![](imgUrl)` 导致在hugo中无法正确显示图片。  
    [Export snippet Markdown ❚ ox-hugo Test Site](https://ox-hugo.scripter.co/test/posts/export%5Fsnippet%5Fmarkdown/)

4.  宏的使用  
    `习七` Issue #[85](https://github.com/it-boyer/it-boyer.github.io/issues/85)

5.  Multiple `ATTR_HTML`  
    snippet命令：img

<!--listend-->

```org
#+html: <style>.foo img { border:2px solid black; }</style>
#+attr_html: :alt Org mode logo
#+attr_html: :width 300 :class foo
[[https://ox-hugo.scripter.co/test/images/org-mode-unicorn-logo.png]]
```

1.  SVG 支持  
    
    ```org
       #+begin_src plantuml :file ../iPics/images/ox-hugo/svg-with-hyperlinks.svg :exports results
       skinparam svgLinkTarget _parent
       start
       :[[https://ox-hugo.scripter.co/ ox-hugo homepage]];
       stop
       #+end_src
       #+caption: An SVG with *hyperlinks* -- generated using PlantUML
       #+attr_html: :inlined t
       #+RESULTS:
       [[file:../iPics/images/ox-hugo/svg-with-hyperlinks.svg]]
    ```

[Inlined SVG ❚ ox-hugo Test Site](https://ox-hugo.scripter.co/test/posts/inlined-svg/)  

问题：  
[homebrew 安装emacs 27.1 不支持svg吗? - Emacs-general - Emacs China](https://emacs-china.org/t/homebrew-emacs-27-1-svg/16451/3)  
[无法显示 svg，Invalid image type 'svg' - Emacs-general - Emacs China](https://emacs-china.org/t/svg-invalid-image-type-svg/17561)  


## <span class="section-num">3</span> org-download工具使用 {#org-download工具使用}

org-download本身会自动在org文档当前目录下创建一个与文档同名的文件夹来保存图片，然后支持多种途径的图片插入，插入之后会复制或者下载一张图片到图片文件夹下面：  

-   用url把图片插入，然后自动下载；
-   复制图片文件路径，然后插入；
-   拖拽图片插入。


### 支持两种 `org-download-method` 方式： {#支持两种-org-download-method-方式}


#### 方式1： `attach` {#方式1-attach}

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
(setq org-download-method 'attach)
{{< /highlight >}}

在这种模式下，存在两个问题  

1.  问题1:在插入图片时，图片会被存到 ~ `/org/.attach` 下一个很长串的目录下。  
    
    这种附件方式，无法指定图片的存放位置，导致无法使用托管图库，导致库文件大。  
    
    解决办法：将 `.attach` 目录作为图库的托管目录，这样就可以把较大的附件托管给子模块库中。

2.  问题2:在插入了attachment后，headline添加一个名为 `:ATTACH:` 的tag  
    
    因为ox-hugo在导出org时，会把headline的tag作为tag或分类，导出到hugo站中。对于hugo来说 `ATTACH` 意义不大。  
    
    解决办法：关闭导出标签的开关 `(setq org-export-with-tags nil)` 这种方式在ox-hugo导出无效。


#### 方式2： `directory` {#方式2-directory}

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
(setq org-download-method 'directory)
{{< /highlight >}}


### doom-emacs下安装 {#doom-emacs下安装}

在init.el中的org模块下增加 `+org-download` .  


### 使用 {#使用}

`spc m a P`: yank  
`spc m a p`: clipboard  
`z i`: 预览图片开关  


### 图片大小控制 {#图片大小控制}

通过下面的属性可以分别控制图片在emacs里面显示的大小以及导出的html的图片的大小。  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
#+ATTR_ORG: :width 200
#+ATTR_HTML: :width 50% :align center
[[attachment:_20210805_170910proxy.png]]
{{< /highlight >}}
