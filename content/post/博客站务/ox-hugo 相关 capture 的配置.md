+++
title = "ox-hugo 使用 org-capture 模版搜集灵感提高效率"
description = "Test post to test another ox-hugo test."
date = 2021-05-11T17:58:00+08:00
publishDate = 2021-05-11T00:00:00+08:00
lastmod = 2021-05-14T17:34:41+08:00
tags = ["hugo"]
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2021-05-12 Wed 10:41]</span></span>

一直在尝试使用 ox-hugo 提升编写博客的效率,苦于资料苦涩难懂,网上资源缺失,一直处于又爱又恨的情绪,导致效率不升反降的尴尬境地.  
今天发奋图强,自我感觉找到了效率密码,尝试在这里做一次分享,希望可以帮助到和我同样处境的小伙伴.  

主要涉及到几个知识点,会分几个小结也说明下:  

-   先看看 ox-hugo 官方放出的 capture 实现方式
-   在看看 org-capture 自定义方式实现,管理更加灵活方便

本人使用的是 `doom` 主题,用得它自带的命令快捷键,和读者的快捷键有可能存在差异.  


## org-capture 基础 {#org-capture-基础}

考虑到对 cupture 不太熟悉的小伙伴,在进入主题之前,先简单科普下 capture,我会贴上官网地址,然后,参考网上的介绍坐下快进.  
[Capture (The Org Manual)](https://orgmode.org/manual/Capture.html#Capture)  
官方介绍: `org-capture` 功能使您可以在不中断工作流程的情况下快速存储笔记。  

-   设置 capture :设置哪些目录下的 org 文件可以使用 capture 功能  
    
    ```elisp
        ((setq var ) org-default-notes-file (concat org-directory "/notes.org"))
    ```
    
    配置好之后,重新加载下配置,    使用这个和自己的快捷键有关,目前使用 doom 主题,所以 `spc shift x` 激活 capture 功能页面


### 模版定义和加载 {#模版定义和加载}

针对不同的 note 样式,定义自己的模版.  
这是重要章节:[Capture templates (The Org Manual)](https://orgmode.org/manual/Capture-templates.html#Capture-templates)  
灵活的模版功能,基于三个特征,使得模版能够灵活满足用户的体验.  

1.  模版成员:[Template elements (The Org Manual)](https://orgmode.org/manual/Template-elements.html#Template-elements)  
    -   keys:绑定键 `("b" "功能描述")`
    -   description: 功能描述.
    -   type:插入的 note 类型,目前支持的类型: `entry`  `item` `checkitem` `table-line` `plain`
    -   target:指定插入的节点位置,支持的关键字 `file` `id` `file+headline` `file+olp` `file+regexp` `file+olp+datetree` `file+function` `clock` `function`
    -   properties:属性, `:immediate-finish` `:jump-to-captured` `:empty-lines` `:clock-in` `:clock-keep`
    -   template:模版的实体它支持文件,字符串,甚至方法来实现  
        
        ```elisp
             (file "/path/to/template-file")
             (function FUNCTION-RETURNING-THE-TEMPLATE)
        ```
2.  模版扩展 `%` 转义符(占位符):[Template expansion (The Org Manual)](https://orgmode.org/manual/Template-expansion.html#Template-expansion)  
    在 capture 模版中,支持 `％` 转义符,动态插入 note 信息.  
    `%[FILE]`:Insert the contents of the file given by FILE.  
    `%(EXP)`:支持表达式,动态添加信息  
    `％<FORMAT>`:FORMAT 规范上的 format-time-string 的结果.  
       `format-time-string`: 指定一时间戳格式化样式[org-time-stamp-custom-formats](https://stackoverflow.com/questions/23218316/org-mode-org-time-stamp-custom-formats-shows-midnight-time)  
    
    ```elisp
       <[%H:%M]> ;;生成的结果是: [17:09]
       <%d %m %Y  %a [%H:%M]> ;;生成的结果是: 02 12 2021 Fir [17:09]
    ```
    
    真实案例:  
    
    {{< highlight elisp "hl_lines=3" >}}
       ("g" "日总结周总结做日志记录、日记写作,file+weektree顺序排列"
                      entry (file+weektree org-agenda-file-gtd)
                      "* %<[%H:%M]> %^{title} %^g\n %?\n"
                      :empty-lines 1
                      ;; :immediate-finish t
                      ;; :jump-to-captured 1
                      )
    {{< /highlight >}}
    
    `％t`:时间戳记，仅日期.  
    `％T`:带有日期和时间的时间戳.  
    `％u` / `％U`:时间  
    `%^g`:提示输入标签，并在预选框中显示目标 org 文件中 tags.  
    `%^G`:提示输入标签，并在预选框中显示所有 org 文件中的所有的 tags。
3.  模版定制:[Templates in contexts (The Org Manual)](https://orgmode.org/manual/Templates-in-contexts.html#Templates-in-contexts)  
    略..
4.  加载模版  
    `org-capture-templates`:属性,定义好模版之后,需要把模版添加到这个属性中才会生效.有两种加载模版的方式  
    1.  初始化时,加载  
        
        ```elisp
              (setq org-capture-templates
                    '(("t" "Todo" entry (file+headline "~/org/gtd.org" "Tasks")
                       "* TODO %?\n  %i\n  %a")
                      ("j" "Journal" entry (file+datetree "~/org/journal.org")
                       "* %?\nEntered on %U\n  %i\n  %a")))
        ```
    2.  借助 `add-to-list` 方法加载  
        
        ```elisp
              (add-to-list 'org-capture-templates
                           '("!l" "Link" entry (file org-agenda-file-inbox)
                             "* TODO 阅读：%:description\nCaptured On: %U\n\n%:link" :immediate-finish t))
        ```


## oh-hugo 官网案例 {#oh-hugo-官网案例}

先放上地址:[Org Capture Setup — ox-hugo - Org to Hugo exporter](https://ox-hugo.scripter.co/doc/org-capture-setup/)  
先介绍下基础功能:获取用户输入的博客名,设置导出文件名:  

```elisp
(let* ((title (read-from-minibuffer "Post Title: ")) ;Prompt to enter the post title
           (fname (org-hugo-slug title)))
```

获取到到文件名 `fname` 之后,会在插入到 org 时,填充给必须属性: `:EXPORT_FILE_NAME:`.  
官网的 capture 主要实现在用湖创建一篇新博客时,会自动填充属性栏中的导出文件名的值,这样用户就可以直接进行博客编写,不需要关心导出时的相关设置.  

自动获取用户输入的内容,作为输入文件路径  

{{< highlight elisp "hl_lines=6-7" >}}
;; Populates only the EXPORT_FILE_NAME property in the inserted headline.
(with-eval-after-load 'org-capture
  (defun org-hugo-new-subtree-post-capture-template ()
    "Returns `org-capture' template string for new Hugo post.
See `org-capture-templates' for more information."
    (let* ((title (read-from-minibuffer "Post Title: ")) ;Prompt to enter the post title
           (fname (org-hugo-slug title)))
      (mapconcat #'identity
                 `(
                   ,(concat "* TODO " title)
                   ":PROPERTIES:"
                   ,(concat ":EXPORT_FILE_NAME: " fname)
                   ":END:"
                   "%?\n")          ;Place the cursor here finally
                 "\n")))
{{< /highlight >}}


<div class="src-block-caption">
  <span class="src-block-number">Code Snippet 1</span>:
  获取subtree名称,设置导出文件名
</div>

然后通过 `add-to-list` 加入到模版列表:  

```elisp
  (add-to-list 'org-capture-template    s
               '("h"                ;`org-capture' binding + h
                 "Hugo post"
                 entry
                 ;; It is assumed that below file is present in `org-directory'
                 ;; and that it has a "Blog Ideas" heading. It can even be a
                 ;; symlink pointing to the actual location of all-posts.org!
                 (file+olp "all-posts.org" "Blog Ideas")
                 (function org-hugo-new-subtree-post-capture-template))))
```

官方推荐了几个属性:  

1.  `:EXPORT_HUGO_BUNDLE:` 导出到 Page Bundle
2.  `:EXPORT_DATE:` 插入时间戳借助脚本获取当前时间,赋值到属性上:  
    
    {{< highlight elisp "hl_lines=4-5 13" >}}
       (defun org-hugo-new-subtree-post-capture-template ()
         "Returns `org-capture' template string for new Hugo post.
       See `org-capture-templates' for more information."
         (let* (;; http://www.holgerschurig.de/en/emacs-blog-from-org-to-hugo/
                (date (format-time-string (org-time-stamp-format :long :inactive) (org-current-time)))
                (title (read-from-minibuffer "Post Title: ")) ;Prompt to enter the post title
                (fname (org-hugo-slug title)))
           (mapconcat #'identity
                      `(
                        ,(concat "* TODO " title)
                        ":PROPERTIES:"
                        ,(concat ":EXPORT_FILE_NAME: " fname)
                        ,(concat ":EXPORT_DATE: " date) ;Enter current date and time
                        ":END:"
                        "%?\n")                ;Place the cursor here finally
                      "\n")))
    {{< /highlight >}}
