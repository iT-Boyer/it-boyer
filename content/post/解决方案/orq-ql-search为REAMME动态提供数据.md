+++
title = "借助 org-ql 工具自动导出 agenda 待办任务"
description = "博客简介"
date = 2023-05-31T01:07:00+08:00
lastmod = 2023-05-31T01:21:42+08:00
tags = ["reader"]
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

需求：在 github 的 README 中显示日常项目状态，显示本周主要的任务，列出项目清单和任务安排。设计：使用 org-ql 查询任务状态，列出项目和代办实现，导出 json 格式。 


## 基于 org-ql-search 实现获取查询，使用 org-ql-view 模板格式化结果数据 {#基于-org-ql-search-实现获取查询-使用-org-ql-view-模板格式化结果数据}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun build-readme-view (name)
  "Choose and display the `org-ql-views' view NAME.
Interactively, prompt for NAME."
  (let* ((result)
         (view (alist-get name org-ql-views nil nil #'string=))
         )
    (-let* (((&plist :buffers-files :query :sort :narrow :super-groups :title) view)
            (super-groups (cl-typecase super-groups
                            (symbol (symbol-value super-groups))
                            (list super-groups))))
      (message "step 1:+++++++ %s" query)
      (build-readme-search buffers-files query
        :super-groups super-groups :narrow narrow :sort sort :title title
        :buffer org-ql-view-buffer)
      ))
  )

(cl-defun build-readme-search (buffers-files query &key narrow super-groups sort title
                                             (buffer org-ql-view-buffer))
  (declare (indent defun))
  (interactive (list (org-ql-view--complete-buffers-files)
                     (read-string "Query: " (when org-ql-view-query
                                              (format "%S" org-ql-view-query)))
                     :narrow (or org-ql-view-narrow (eq current-prefix-arg '(4)))
                     :super-groups (org-ql-view--complete-super-groups)
                     :sort (org-ql-view--complete-sort)))
  ;; NOTE: Using `with-temp-buffer' is a hack to work around the fact that `make-local-variable'
  ;; does not work reliably from inside a `let' form when the target buffer is current on entry
  ;; to or exit from the `let', even though `make-local-variable' is actually done in
  ;; `org-ql-view--display'.  So we do all this within a temp buffer, which works around it.
  (with-temp-buffer
    (let* ((query (cl-etypecase query
                    (string (if (or (string-prefix-p "(" query)
                                    (string-prefix-p "\"" query))
                                ;; Read sexp query.
                                (read query)
                              ;; Parse non-sexp query into sexp query.
                              (org-ql--query-string-to-sexp query)))
                    (list query)))
           (results (org-ql-select buffers-files query
                      :action 'element-with-markers
                      :narrow narrow
                      :sort sort))
           (strings (-map #'org-ql-view--format-element results))
           (buffer (or buffer (format "%s %s*" org-ql-view-buffer-name-prefix (or title query))))
           (header (org-ql-view--header-line-format
                    :buffers-files buffers-files :query query :title title))
           ;; Bind variables for `org-ql-view--display' to set.
           (org-ql-view-buffers-files buffers-files)
           (org-ql-view-query query)
           (org-ql-view-sort sort)
           (org-ql-view-narrow narrow)
           (org-ql-view-super-groups super-groups)
           (org-ql-view-title title))
      (when super-groups
        (let ((org-super-agenda-groups (cl-etypecase super-groups
                                         (symbol (symbol-value super-groups))
                                         (list super-groups))))
          (setf strings (org-super-agenda--group-items strings))))
      ;;TODO no show view
      ;; insert buffer to coverto build readme
      (message "build readme file:\n %s " (s-join "\n" strings))
      ;; (org-ql-view--display :buffer buffer :header header :string (s-join "\n" strings))
                )))

(build-readme-view "readme")
```

```text
build readme file:

  原则
  TODO 诚信原则
  TODO 成长原则
  TODO 自我管理原则

 基于 org-super-agenda 定义 agenda 模板
  TODO 快速导出 agenda 清单模板  9d ago  :@学习笔记:
  TODO 每日安排  :@学习笔记:
  TODO 每周安排  :@学习笔记:
```


## 基于 org-ql-select 获取查询结果，自定义 list-to-org 格式化结果内容 {#基于-org-ql-select-获取查询结果-自定义-list-to-org-格式化结果内容}

借助 org-ql-search 和 org-ql-view 能够实现 


### 自定义保存 org-ql-views 模板 {#自定义保存-org-ql-views-模板}

保存不同查询的模板，保存之后，可以通过 org-ql-view 命令，快速查看模板列表。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun org-build-readme-save ()
  "create new view"
  (interactive)
  (let* ((name (read-string "Save view as: " org-ql-view-title))
         ;; Bind `org-ql-view-title' to the name that was read, in case
         ;; it's different, which `org-ql-view--plist' will pick up.
         (org-ql-view-title name)
         (plist (list :buffers-files (org-agenda-files)
                      :query '(todo "TODO")
                      :sort '(date priority)
                      :title "This week"
                      :super-groups '((:auto-parent t))
                      )))

    (when (or (not (map-elt org-ql-views name nil))
              (yes-or-no-p (format "Overwrite view \"%s\"?" name)))
      (setf (map-elt org-ql-views name nil #'equal) plist)
      (customize-set-variable 'org-ql-views org-ql-views)
      (customize-mark-to-save 'org-ql-views))))
(org-build-readme-save)
```


### 实现 org-ql-select 支持 org-ql-views 模板中的 query 查询 {#实现-org-ql-select-支持-org-ql-views-模板中的-query-查询}

1.  `org-readme-search` 参考 org-ql-view 和 org-ql-search 简化搜索逻辑，实现基于 org-ql-select 方法实现自定义查询的效果。通过传入 org-ql-views 模板的名称，利用模板的 `query` 属性的查询条件。结果还是 list 格式。想要输出格式的内容，需要自定义方法 `list-to-org` 打印。
2.  `list-to-org` 将 org-ql-select 查询到的 elisp list 列表格式，转为 org 格式。

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }

(defun list-to-org (list)
  "Convert LIST to org-mode string."
  (mapconcat
   (lambda (elem)
     (cond ((stringp elem) elem)
           ((keywordp elem) (concat ":" (symbol-name elem)))
           ((listp elem) (list-to-org elem))
           (t (format "%S" elem))))
   list
   "\n"))

(defun org-readme-search (name)
  "Choose and display the `org-ql-views' view NAME.
Interactively, prompt for NAME."
  (let* ((result)
         (view (alist-get name org-ql-views nil nil #'string=))
         )
    (-let* (((&plist :buffers-files :query :sort :narrow :super-groups :title) view)
            (super-groups (cl-typecase super-groups
                            (symbol (symbol-value super-groups))
                            (list super-groups))))
      (message "step 1:+++++++ %s" query)
      (let* ((query (cl-etypecase query
                      (string (if (or (string-prefix-p "(" query)
                                      (string-prefix-p "\"" query))
                                  ;; Read sexp query.
                                  (read query)
                                ;; Parse non-sexp query into sexp query.
                                (org-ql--query-string-to-sexp query)))
                      (list query)))
             )

        (message "step 2:_++++++_ %s" query)
        (setq result (org-ql-select buffers-files query
                      :action '(list
                               ;; (org-get-heading)
                               (buffer-substring-no-properties (point-at-bol) (point-at-eol))
                               )
                      :narrow narrow
                      :sort sort))
        ))

    (message "reuslts : %s" (list-to-org result))
    ))

(org-readme-search "readme")
```

```text
reuslts : **** TODO 诚信原则
**** TODO 成长原则
**** TODO 自我管理原则
*** TODO [#A] 二象限模式[0/3] :habit:
*** TODO 健身模式 :habit:
*** TODO [#B] 工作模式 :habit:
*** TODO [#B] 阅读模式 :habit:
*** TODO [#B] 饭桌礼仪 :habit:
*** TODO [#B] 社交模式 :habit:
* TODO 思考最重要的事情撰写使命宣言 :blue:
* TODO [#B] 拓宽思路的过程 :anki:
** TODO 开发 auto-gpt emacs 插件
** TODO 捷径通过 emacsclient 命令，或 ifff 集成 Auto-GPT
**** TODO 待完善的模板
*** TODO ob-babel 导入第三库遇到的问题
**** TODO Macro: =org-ql-defpred=
**** TODO 每日安排
**** TODO 每周安排
**** TODO 快速导出 agenda 清单模板
**** TODO cowuoshi
**** TODO 开始计划
**** TODO 明天到家
```


## 替换 REAME.md 模块数据 {#替换-reame-dot-md-模块数据}

在 `README.md` 中有固定模式，使用 elisp 处理围绕文本的内容替换为自己的内容。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
<!-- dairy starts -->

<!-- dairy ends -->
```

python 使用正则实现的替换方式 `replace_chunk`: 

```python { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
def replace_chunk(content, marker, chunk, inline=False):
    r = re.compile(
        r"<!\-\- {} starts \-\->.*<!\-\- {} ends \-\->".format(marker, marker),
        re.DOTALL,
    )
    if not inline:
        chunk = "\n{}\n".format(chunk)
    chunk = "<!-- {} starts -->{}<!-- {} ends -->".format(marker, chunk, marker)
    return r.sub(chunk, content)
```


### 将上述方法翻译成 elisp 语法实现 {#将上述方法翻译成-elisp-语法实现}


#### 使用 temp-buffer 实现文本替换 {#使用-temp-buffer-实现文本替换}

```elisp
(defun build-readme-replace-chunk (content marker chunk &optional inline)
  "Replace the chunk between <--- MARKER starts --> and
<!--- MARKER ends --> in CONTENT with CHUNK.
If INLINE is non-nil, do not add newlines around CHUNK."
  (let* ((start (concat "<!-- " marker " starts -->"))
        (end (concat "<!-- " marker " ends -->"))
        (pattern (concat start "[^^]*?" end)))
    (with-temp-buffer
      (insert content)
      (goto-char (point-min))
      (if (re-search-forward pattern nil t)
          (progn
            (setq chunk (format "%s\n%s\n%s" start chunk end))
            (replace-match chunk t t)
            (buffer-string))
        content))
    ))

(setq readme-content "
Hello
<!-- chunk starts -->
world
<!-- chunk ends -->
!")

(build-readme-replace-chunk readme-content "chunk" "there")
```

```text

Hello
<!-- chunk starts -->
there
<!-- chunk ends -->
!
```

它的工作原理如下: 

-   它定义了 `start` 和 `end` 字符串,即起始和结束标记。
-   它定义了一个正则表达式模式,能匹配从起始标记到结束标记的内容。
-   它创建一个临时缓冲区并插入 `content` 内容。
-   它向前搜索第一个模式匹配。
-   如果找到匹配,它用 `chunk` 替换匹配内容,并可选地添加换行符。
-   然后它返回临时缓冲区的内容,替换了匹配内容。
-   否则它只返回原始的 `content` 不变。


#### 使用 repace-regexp-in-string 方法替换 {#使用-repace-regexp-in-string-方法替换}

这是将 Python 方法 `replace_chunk` 翻译成 Emacs Lisp 语法的示例： 

```emacs-lisp
(defun replace-chunk-regex (content marker chunk &optional inline)
  "Replace content between markers denoted by MARKER with CHUNK.
If INLINE is non-nil, do not add extra newline characters around CHUNK."
  (let ((regex (concat "<!-- " marker " starts -->[^^]*<!-- " marker " ends -->")))
    (setq chunk (if inline chunk (concat "\n" chunk "\n")))
    (setq chunk (concat "<!-- " marker " starts -->" chunk "<!-- " marker " ends -->"))
    (replace-regexp-in-string regex chunk content t t)))

(replace-chunk-regex readme-content "chunk" "there")
```

```text

Hello
<!-- chunk starts -->
there
<!-- chunk ends -->
!
```

这个函数首先定义了一个正则表达式来匹配和找到要替换的文本。然后，根据 `inline` 参数，添加或去掉块的前后额外换行符。最后，使用 Emacs Lisp 内置函数 =replace-regexp-in-string=，将找到的文本块替换为新的文本块。 


### 替换文本之后保存到 README.md 文件中 {#替换文本之后保存到-readme-dot-md-文件中}

使用 elisp 脚本替换 README.md 文件中{{内容}} 格式的内容为变量A的值，然后保存文件。 

定义一个入参 `readmeFile` 在 temp-buffer 中操作该文件。 

```elisp
(defun build-readme-replace (readmeFile marker chunk &optional inline)
  "Replace the chunk between <--- MARKER starts --> and
<!--- MARKER ends --> in CONTENT with CHUNK.
If INLINE is non-nil, do not add newlines around CHUNK."
  (let* ((start (concat "<!-- " marker " starts -->"))
        (end (concat "<!-- " marker " ends -->"))
        (pattern (concat start "[^^]*?" end)))
    (with-temp-file readmeFile
      (insert-file-contents readmeFile)
      ;;(insert-buffer-substring (current-buffer))
      (goto-char (point-min))
      (if (re-search-forward pattern nil t)
          (progn
            (setq chunk (format "%s\n%s\n%s" start chunk end))
            (replace-match chunk t t)
            (write-file readmeFile))
        ))
    ))
(build-readme-replace "~/hsg/iNotes/README.md" "dairy" " test text")
```

```text
t
```

这个函数的工作方式如下: 

-   它提示用户输入要保存的文件名,并将其存储在 `readmeFile` 变量中
-   它获取当前缓冲区,并将其存储在 `buffer` 变量中
-   它使用 `with-temp-buffer` 宏创建一个新的临时缓冲区
-   它使用 `insert-file-contents` 将 `readmeFile` 的内容插入临时缓冲区
-   它使用 `write-file` 将临时缓冲区内容写入 `readmeFile` 文件

这里希望直接保存内容到文件中，不需要提示，在使用 `save-buffer` 方式保存时，会显示一条确认消息,确认已将缓冲区保存到指定的文件名

