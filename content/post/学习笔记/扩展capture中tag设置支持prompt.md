+++
title = "扩展 org-capture 基于 TAGS 功能动态设置 prompt"
description = "博客简介"
date = 2023-05-20T09:47:00+08:00
lastmod = 2023-05-20T09:50:31+08:00
tags = ["灵感"]
categories = ["日志随笔", "学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

在 capture 中支持 tag 的选择，tag 可以分类，扩展AI分类，以 prompt 的 title 作为 tag 值。在调用 gpt 之前，只需要设定 tag 就行了。 

例如：想使用 写作助理的角色，完成一下操作 

1.  现在 tag 分组中添加角色名称
2.  再添加 tag 到 headline 上
3.  在请求之前，使用 tag 值匹配 prompt 模块的名称，然后就可以为 gptel 设置 system 了。

难点： 如何通过 tag 获取 prompt，有几种方案 

1.  依赖 json 文件，通过解析 json 文件，匹配到 prompt
2.  直接从 prompt.org 文件中获取 
    1.  手动解析 org-element 数据，在进行匹配操作
    2.  借助第三方库 org-ql 实现快速检索 org 文件的内容，获取到 prompt

在支持 tag 功能之后，将很大的改进 gptel 使用场景： 

1.  在 capture 中，就可以通过 tag 方式,设置 prompt 。
2.  在 org 文件时，当前光标的位置调用 gptel ，只要设置 tag , gptel 就以设置的 prompt 方式回答问题。

需要做那些工作 

-   [X] 使用 org-ql 查询 tag 匹配的 prompt
-   [X] 使用 org-ql 获取 prompt 库中内容
-   [ ] 替换 gptel 自定义方法中，调试功能 
    
    在继承过程中发现遗留问题： 
    
    1.  org-ql query 的 where 语法中, 例如：提示type格式问题。 
        ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
               (setq type "hello world")
               (org-ql-query :from "file"
                             :where '（heading type))
        ```
    2.  使用 `org-get-entry` 获取条目内容职能得到第一行的内容
    3.  从 capture 中无法拿到 tags ,目前通过添加 org-capture 属性的传递模板类型。意味着无法在 capture 过程中，使用 tag 设置的机制，设置 prompt。


## 使用 org-ql 解析 prompt.org 数据 {#使用-org-ql-解析-prompt-dot-org-数据}

以下面格言书 prompt 格式为例子，获取 prompt 子目录 system 条目中的内容 

```org { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
* 格言书 :文本:AI:
:PROPERTIES:
:remark: 根据问题输出鼓舞人心的名言和有意义的格言。
:END:

** system
我希望你能充当一本箴言书。对于我的问题，你会提供明智的建议、鼓舞人心的名言和有意义的谚语，以帮助指导我的日常决策。此外，如果有必要，你可以提出将这些建议付诸行动的实际方法或其他相关主题。
** user
我的问题是:
```

[org-ql/READ-zh.org](https://github.com/alphapapa/org-ql//tree/3d24146ddaa47e1ae3ca6cae89b8bb3d143cc9ec/READ-zh.org) 

[org-ql/examples.org](https://github.com/alphapapa/org-ql//tree/3d24146ddaa47e1ae3ca6cae89b8bb3d143cc9ec/examples.org) 

在学习 org-ql 的基本用法，需要使用 org-ql 的 ancestors 特性： 

子节点条件语法： (ancestors (heading "tag名称")),该表达式是要求匹配的条目必须有父级条目，使用嵌套语法要求父条目名称为 tag 的名称。 

这样 select 返回的是 system 条目的 org-element 元数据。 

再通过 select 语句明确要返回条目的内容类型,例如获取 system 条目下的文本类型: `:select '(org-get-entry)` 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-ql-query
  :select '(org-get-entry)
  :from "~/hsg/iNotes/content-org/prompt/prompt.org"
  :where '(and (tags "AI")
               (ancestors (heading "内容总结"))
               (heading "system")
               ))
```

|                                                   |
|---------------------------------------------------|
| 请将我提供的每篇文字都概括为 100 个字，使其易于阅读和理解。避免使用复杂的句子结构或技术术语。 |

下面根据具体场景,将上面的查询方法，封装一下获取 prompt 目录下 system 和 user 的内容. 


#### 获取字符串一级条目的 AI 标签 {#获取字符串一级条目的-ai-标签}

在实际需求中，需要先从 capture 的字符串的内容中获取用户设置的 AI 标签，然后提供给处理 prompt.org 使用。 

这个方法主要两个参数： 

1.  org-str 是 capture 过程中的 org 字符串。
2.  tagregex 是 org-ql 查询用户设置的 tag 正则，匹配到 AI 标签，并返回。

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun get-element-tag-by-ql (org-str tagregex)

  (let (result)
    (with-current-buffer (get-buffer-create "*gpteltagtemp*")
      (erase-buffer)
      (insert org-str)
      (org-mode)
      (message "buffer内容： %s \n buffer 名称： %s" (buffer-string) (current-buffer))
      (setq tags (org-ql-query
                   :select '(org-entry-get (point) "TAGS")
                   :from (current-buffer)
                   :where '(and
                            (tags* (format "^%s.*$" tagregex))
                            (not (parent))
                            )
                   ))
      (message "数组1111：%s" tags)
      (setq result (catch 'tag-found
                     (dolist (tag tags)
                       (setq tag (substring tag 1 -1))   ; 去除首尾:
                       (setq tagarr (split-string tag ":"))
                       (dolist (item tagarr)
                       (message "匹配中：%s" item)
                       (setq reg (format "^%s.*$" tagregex))
                       (message "正则：%s" reg)
                         (when (string-match reg item)
                           (setq item (substring item 2)) ;;去除AI前缀
                           (throw 'tag-found item)))
                       )))
      (message "结果：%s" result)
      )))
(setq org-string "* Hello world :AI内容总结: \nThis is the body of first-level headline.\n\n** Subheadline \nTest text.Test text.")
(get-element-tag-by-ql org-string "AI")
```

```text
结果：内容总结
```


#### 通过 AI 标签从 prompt.org 文件中获取 prompt {#通过-ai-标签从-prompt-dot-org-文件中获取-prompt}

使用 org-ql 从 prompt.org 文件中获取 prompt，封装了如下方法，为了便于测试，使用 with-temp-buffer 

方法的作用和参数 

1.  第一参数，为 prompt.org 文件路径
2.  第二参数，来自 capture 中用户设置的 AI 标签，对应 prompt.org 条目中一级条目的标题。
3.  第三参数，设置 prompt 的类型，支持 system 和 user 类型，对应 prompt.org 中的子条目的title。定义 ptype 过程中，遇到 (heading ptype) 报错的问题，没有排查到原因，展示使用 1 和 其他标识来区分要获取的类型。

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun get-element-prompt-by-ql (org-str title ptype)
  (let (prompt)
    (with-current-buffer (get-buffer-create "*gpteltemp*")
      (erase-buffer)
      (insert org-str)
      (org-mode)
      (if (= ptype 1)
          (setq prompt (org-ql-query
                         :select '(org-get-entry)
                         :from (current-buffer)
                         :where '(and
                                  (ancestors (heading title))
                                  (heading "system")
                                  )))
        (setq prompt (org-ql-select  (current-buffer)
                       '(and
                         (ancestors (heading title))
                         (heading "user")
                         )
                       :action '(org-get-entry)
                       ))
        ;
        )
      (message "当前获取的类型：%s" (car prompt))
      )))
(setq org-string "* Hello world :AI:\nThis is the body of first-level headline.\n** user\n\nTest text.Test text.")
;; (setq file "~/hsg/iNotes/content-org/prompt/prompt.org")
(setq file "~/hsg/iNotes/content-org/test.org")
(get-element-prompt-by-ql org-string "Hello world" 0)
```

```text
当前获取的类型：
Test text.Test text.
```

