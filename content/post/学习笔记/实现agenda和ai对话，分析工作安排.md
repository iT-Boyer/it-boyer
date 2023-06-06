+++
title = "使用 ChatGPT 分析优化 agenda 日程安排"
description = "博客简介"
date = 2023-05-27T20:08:00+08:00
lastmod = 2023-05-27T20:10:52+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

借助 AI 帮助分析日程安排的也将是非常有力的工具。 

1.  如何获取 agenda 的内容,直接返回构建的视图内容字符串,而不跳转到 agenda 缓冲区 
    -   [ ] 通过 org-agenda 内置命令导出文本格式使用自带命令，会跳转到 org-agenda-view 中无法在 elisp 中拿到列表数据
    -   [X] 通过 org-ql 查询命令获取 agenda 列表
2.  [X] 将内容投喂给 gptel 获取分析报告和建议借助 org-roam-capture 通过 tag 设定 prompt 角色，在完成 capture 时，自动对话AI。
3.  [X] 优化 prompt 对话模板: 
    1.  基于 capture 模板: `内容总结` ， `写作助理`
    2.  基于 snippet 模板： `agd` 调用自定义方法，实现查询 org 内容，插入到当前 buffer


## 使用 org-ql 获取TODO数据实现 AI 对话 {#使用-org-ql-获取todo数据实现-ai-对话}

获取 org-agenda 的清单数据。在获取到数据之后，就可以为其他自定义方法提供数据支持。 

例如，想要将查询到的数据，插入当前 buffer，先将列表数据以换行的方式格式化为字符串,然后插入当前位置 buffer。 

还可以结合 gptel 接口，将查询的内容投喂给 AI ，实现和 AI 的对话，例如帮助分析 agenda 任务安排的合理性，获取 AI 专业的建议。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun org-ql-week-todo ()
  (let ((result ""))
    (setq result (org-ql-query
                   :select '(org-get-heading)
                   ;; :from (org-agenda-files)
                   :where '(todo)))

    (setq content (s-join "\n" (mapcar #'(lambda (x) (format "%s" x)) result)))
    (message "搜索结果：\n%s" content)
    )
  )
(insert-org-ql-todo)
```

```text
搜索结果：
TODO 思考最重要的事情撰写使命宣言 :blue:
TODO [#B] 拓宽思路的过程 :anki:
TODO 开发 auto-gpt emacs 插件
TODO 捷径通过 emacsclient 命令，或 ifff 集成 Auto-GPT
TODO 使用 gptel 分析 agenda 优化日程安排
TODO org-batch-store-agenda-views
TODO org-agenda-list-stuck-projects
TODO 每日安排
TODO 每周安排
TODO 快速导出 agenda 清单模板
```


## 介绍 org-agenda 内置导出工具 {#介绍-org-agenda-内置导出工具}

org-agenda 提供三个宏函数： 

1.  `org-batch-agenda` : 为命令行设计，能够执行 org-agenda 模板命令 , `org-batch-agenda-csv` 支持导出 csv 格式
2.  `org-batch-store-agenda-views` : 基于 org-agenda 模板中设置的导出文件格式，批量导出并生成相关文件。
3.  `org-agenda-list-stuck-projects` : 导出阻塞的任务清单。

`elisp` 不执行宏调用的参数;所以不支持函数类型的入参。 

用户如果 `emacsclient` 从命令中获取 agenda 的日程清单。例如，导出7天的议程: 

```sh { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
emacs -batch -l ~/.emacs -eval '(org-batch-agenda "a" org-agenda-span 7)'
```

如果想把 `org-batch-agenda` 作为常规函数调用，则必须使用单引号 `'` 包括。 

此外:使用符号作为参数值，则必须对该符号加双引号。 

[Extracting Agenda Information (The Org Manual)](https://orgmode.org/manual/Extracting-Agenda-Information.html) 


### org-batch-agenda 的用法 {#org-batch-agenda-的用法}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-batch-agenda "a" org-agenda-span 7)
```

```text
 2:00      S-42  TODO 思考最重要的事情撰写使命宣言 :@Inbox::blue:
 2:00      S-42  TODO [#B] 拓宽思路的过程 :@Inbox::anki:
 2:00      D-42  TODO 思考最重要的事情撰写使命宣言 :@Inbox::blue:
 2:00      D-42  TODO [#B] 拓宽思路的过程 :@Inbox::anki:
```

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; (org-batch-agenda "E" org-agenda-span 7)
(setq agenda-result (org-batch-agenda "E" org-agenda-span 7))
(message "每日安排： %s" agenda-result)
```

```text
每日安排： 
--------重要紧急----------

  habit:      TODO [#A] 二象限模式[0/3] :habit:
  project:    TODO [#A] hackingWithSwift

--------重要不紧急---------

  inbox:      TODO [#B] 拓宽思路的过程 :@Inbox::anki:
  inbox:      TODO 开发 auto-gpt emacs 插件 :@Inbox:灵感::
  inbox:      TODO 捷径结合 emacsclient 命令，或使用 ifff 集成 Auto-GPT :@Inbox:灵感::
  inbox:      TODO 使用 gptel 分析 agenda 优化日程安排 :@Inbox::
  inbox:      TODO org-agenda-list-stuck-projects :@Inbox::
  inbox:      TODO org-batch-store-agenda-views :@Inbox::

-------紧急不重要-----------
```


### org-batch-store-agenda-views {#org-batch-store-agenda-views}

基于 org-agenda 模板中设置的导出文件格式，批量导出并生成相关文件。 

定时导出agenda view 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; https://mudan.wiki/Emacs/The_Org_Manual/The_Org_Manual.html
(after! org-agenda
  (setq org-agenda-exporter-settings
        '((ps-number-of-columns 2)
          (ps-landscape-mode t)
          (org-agenda-add-entry-text-maxlines 0) ;;设置导出时，显示任务的内容最大行数
          (org-agenda-prefix-format " [ ] ")     ;; 设置任务title的前缀
          ;;(org-agenda-with-colors nil)   ; 默认值t:显示颜色
          (org-agenda-remove-tags t)
          ;;(org-agenda-tag-filter-preset (list "+personal"))
          (htmlize-output-type 'css)))


  ;; 定时导出agenda view.
  ;;  (file-expand-wildcards "~/org/*.org")
  ;;  (setq org-agenda-files (file-expand-wildcards "~/org/*.org"))
  (defun progo-run-agenda-store ()
    "定时导出agenda"
    (message "Agenda to be exported... ")
    (org-batch-store-agenda-views
     org-agenda-span (quote month)
     ;;org-agenda-start-day "2007-11-01"
     ;;org-agenda-include-diary nil
     org-agenda-files (file-expand-wildcards "~/hsg/iNotes/content-org/Orgzly/*.org"))
    (message "Agenda exported!"))
  ;; 从现在开始，每5分钟：5*60s 执行一次。
  ;;(run-at-time nil 300 'progo-run-agenda-store)
)
```


### org-agenda-list-stuck-projects {#org-agenda-list-stuck-projects}

导出阻塞的任务清单 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-agenda-list-stuck-projects
 7                     ; Next 7 days
 nil                 ; No specific files
 nil                 ; No custom command
 "Tasks"             ; Heading for first view
 '("PROJECT")        ; Only PROJECT todos
 nil nil             ; Default time range
 "Projects")         ; Heading for second view
```

