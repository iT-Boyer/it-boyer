+++
title = "学习ox-hugo自定义todo状态的css样式"
description = "通过定位org-todo关键字找到定制css样式的过程记录，了解elisp代码的用法尝试自定义css样式。"
date = 2021-10-28T15:17:00+08:00
publishDate = 2021-10-28T12:31:00+08:00
lastmod = 2021-11-24T10:55:50+08:00
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

## hugo 开启支持自定义css {#hugo-开启支持自定义css}

在长期使用org管理项目导出hugo博客时，在ox-hugo文档中有设置todo状态的方法。  

但是放在hugo效果中无法呈现逾期效果，经过一番查找，需要设在hugo配置文件中设置支持html自身css样式的加载，否则，hugo 会生成 `<!-- raw HTML omitted -->` html标签，禁止用户自定义样式。  

1.  todo-css 无效果问题解决  
    
    {{< highlight toml "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       [markup]
         [markup.goldmark]
           [markup.goldmark.renderer]
             unsafe = true
    {{< /highlight >}}
    
    [twitter bootstrap - Hugo shortcode ignored saying "raw HTML omitted" - Stack ...](https://stackoverflow.com/questions/63198652/hugo-shortcode-ignored-saying-raw-html-omitted)


## 借助org嵌套语法，添加css样式 {#借助org嵌套语法-添加css样式}

在ox-hugo文档中介绍css加载的方法，经过一几番测试，在不同节点和宏 `#include` css 到博客中，都无法达到预期的效果。  

1.  声明css 样式节点  
    
    重点：设置 `CUSTOM_ID` 属性：todo-css  
    
    有了 `CUSTOM_ID` 值，就可以通过org嵌套语法 `#+include` ，在其他节点嵌套该节点的内容。  
    
    例如：在 `all-posts.org` 文件中，创建一个 `CSS for TODO` 节点，专门放置css 样式。  
    
    {{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       ** CSS for TODO                                                 :noexport:
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

2.  使用 `#+include:` 嵌套语法，在 `project.org` 文件中嵌套 `CSS for TODO` 中css 样式内容。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       #+include: "./all-posts.org::#todo-css" :only-contents t
    {{< /highlight >}}

3.  org-hugo 导出博客后，查看博客html 效果和源码  
    
    {{< highlight html "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       <span class="org-todo done DONE">DONE</span> 人脸识别
    {{< /highlight >}}


## ox-hugo.el高级定制 {#ox-hugo-dot-el高级定制}

在设置了hugo支持绘制css样式之后，就想让博客支持更多的cancel状态的下的样式，下一个问题是如何扩展ox-hugo支持自定义更多的样式问题。  

经过查看ox-hugo 源码，在ox-hugo.el 文件中的方法 `org-hugo--todo` 可以对状态类型扩展。  

下面代码会在导出html 时，对节点添加的css 样式，在第19 行可以看出，目前支持 `done` ， `todo` 两种样式的设置  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=19-19 0-0" >}}
;;;; TODO keywords
(defun org-hugo--todo (todo info)
  "Format TODO keywords into HTML.

This function is almost like `org-html--todo' except that:
- An \"org-todo\" class is always added to the span element.
- `org-hugo--replace-underscores-with-spaces' is used to replace
  double-underscores in TODO with spaces.

INFO is a plist used as a communication channel."
  (when todo
    (message "[DBG todo] todo: %S" todo)
    (message "[DBG todo] org-done-keywords: %S" org-done-keywords)
    (message "[DBG todo] org-todo-keywords: %S" org-todo-keywords)
    (message "[DBG todo] is a done keyword? %S" (member todo org-done-keywords))
    (message "[DBG todo] is a todo keyword? %S" (member todo org-todo-keywords))
    (message "[DBG todo] html-todo-kwd-class-prefix: %S" (plist-get info :html-todo-kwd-class-prefix))
    (format "<span class=\"org-todo %s %s%s\">%s</span>"
            (if (member todo org-done-keywords) "done" "todo")
            ;; (cond
             ;; ((member todo org-done-keywords)
              ;; "done")
             ;; ((member todo org-todo-keywords)
              ;; "todo")
             ;; (t
              ;; "cancel"))
            (or (org-string-nw-p (plist-get info :html-todo-kwd-class-prefix)) "")
            (org-html-fix-class-name todo)
            (org-hugo--replace-underscores-with-spaces todo))))
{{< /highlight >}}


## 要事第一，制定下一步行动 {#要事第一-制定下一步行动}

记录一下折腾笔记，确认要使用的样式，杜绝在小事上浪费精力。  


## 借助hugo配置加载自定义css样式 {#借助hugo配置加载自定义css样式}

解决使用 `#include:` 方式嵌套css 样式，导致文章在主页中显示css 文本。  

1.  在 `hugodir/static/css/` 目录中创建todo.css 文件  
    
    {{< highlight css "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
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
    
       .org-todo.cancel {
           color: blue;
       }
    {{< /highlight >}}
2.  `customCSS` 参数加载自定义的css 样式  
    
    {{< highlight yaml "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       customCSS = ["todo.css"]
    {{< /highlight >}}
3.  效果  
    
    {{< highlight html "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       <link rel="stylesheet" href="/css/todo.css">
    {{< /highlight >}}
