+++
title = "基于 helm-org-ql 自定义 helm 检索 prompt.org 库"
description = "博客简介"
date = 2023-05-25T03:59:00+08:00
lastmod = 2023-05-25T16:54:15+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## helm 功能 {#helm-功能}

Helm是一个Emacs的框架,用于创建符合人体工程学的交互式工具。它有以下主要优点: 

1.  简单且一致的交互方式 
    
    使用相同的界面和按键绑定为许多功能提供接口,这样用户只需要学习一次就可以应用到所有 Helm 工具上。这大大降低了学习成本,让 Emacs 的各种功能更易于掌握。
2.  强大的过滤机制使用实时过滤,可以根据输入的字串快速过滤出相关选项,这使其非常适合处理长列表或大量数据。过滤器可以针对候选项的任何部分(标题,路径,标签等)进行操作。
3.  支持模糊匹配 
    
    过滤不但支持精确匹配,还支持模糊匹配。这在处理长列表时提供了很大便利,只需要输入部分字母就可以过滤出相关选项。
4.  快速选择 
    
    通过字母导航, Helm 允许使用户快速选择列表中的选项。无需从列表顶部逐一翻阅,用户可以直接输入选项的首字母快速定位。
5.  支持补全 
    
    输入支持 Emacs 自带的补全功能。当用户输入部分字母时,Helm 会显示相关选项的补全提示,方便用户快速选择需要的项。
6.  可定制 
    
    具有高度的可定制性,用户可以轻松定义新的 Helm 工具以满足特定需要。许多其他 Emacs 包都提供了 Helm 接口,这使得 Helm 的实用范围更加广泛。
7.  基于源 
    
    工具基于多个命名源(source)运行,每个源返回一个候选项列表。这使得 Helm 可以同时搜索和过滤来自多个源的选项,比如查找文件时同时搜索多个目录。


## 学习路径 {#学习路径}

在日常使用中，helm 是接触最多的，能够很方便提供，现在通过插件的自定义，扩展 prompt 查询和获取 prompt 的功能。 

现在根据比较常用的 helm 工具，可以基于现有的插件做集成，或学习自定义 prompt helm 插件。 

1.  直接调用 helm 传入参数
2.  基于 helm-org-ql 扩展支持 prompt 查询。
3.  参考 helm-org-ql-views 的用法，学习 helm 数据源等，高级用法
4.  参考 helm-org-capture-templates , 扩展 capture 模板的 helm ，学习相关用法


## helm 数据源初始化方法 {#helm-数据源初始化方法}

1.  `helm-make-source` 参考 `helm-org-ql-views-source` 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       ;;;###autoload 通过 org-ql 获取数据源
         ;; 通过 alist 列表中获取数据源
         (defvar helm-org-ql-views-source
           (helm-make-source "Org QL Views" 'helm-source-sync
             :candidates (lambda ()
                           (->> org-ql-views
                                (-map #'car)
                                (-sort #'string<)))
             :action (list (cons "Show view" #'org-ql-view)))
           "Helm source for `org-ql-views'.")
    ```
2.  helm-build-in-buffer-source 参考获取浏览器书签的方法 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defvar helm-chrome-history-source
         (helm-build-in-buffer-source "Edge History"
           :init #'helm-chrome-history-init
           :action (helm-make-actions
                    "Browse URL"
                    (lambda (x) (browse-url (car (split-string x "|"))))
                    "W3m URL"
                    (lambda (x) (w3m (car (split-string x "|")))))))
    ```


### 两个几个属性 {#两个几个属性}

1.  `:candidates` 候选列表: helm 展示的文本和选择传递的内容，基于该项的格式定义。 
    
    1.  简单的候选项列表: `:candidates '("Item 1" "Item 2" "Item 3")`
    2.  候选项的assoc list: 
        ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
              :candidates '(("Item 1" . 1)
                            ("Item 2" . 2)
                            ("Item 3" . 3))
        ```
    
    在这种情况下,每个候选项都是一个 cons cell ,car 部位显示在 helm buffer 中,cdr部位是该候选项的值。
2.  `:action` 选中执行的事件: 支持 闭包的等实现。

自定义 helm 数据源主要基于 org 文件文件中，需要了解 org-ql 的语法，知道如何将会 org-ql 结果数据，转换为 helm 的数据源等。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq contents (org-ql-query
                   :select '(org-get-entry)
                   :from "~/hsg/iNotes/content-org/prompt/prompt.org"
                   :where '(and
                            (ancestors (heading gts-claude-session))
                            (heading "system")
                            )))
```


## 基于 helm-org-ql 设置 AI prompt {#基于-helm-org-ql-设置-ai-prompt}

1.  声明处理 AI prompt 函数 `helm-org-ql-AI-show-marker`
2.  添加到 `helm-org-ql-actions` 中。
3.  调用 `helm-org-ql` 时，在选中条目时，Tab 键，跳转到actions 操作列表。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       ;; AI 选择Prompt 获取 system 的内容
       (add-to-list 'helm-org-ql-actions  (cons "设置为 Claude 角色" 'helm-AI-prompt-marker))
       ;; 为helm-org-ql 添加自定义 action
       ;; 在 helm view 选择条目之后，处理条目下的内容获取。
       (defun helm-AI-prompt-marker (marker)
         "Show heading at MARKER."
         (interactive)
         (let ((role "")
               (prompt "")
               (start (marker-position marker)) ;;标记起始位置
               )
           (message "开始位置-----：%s" start)
           (with-current-buffer (marker-buffer marker)
             ;; (message "获取entry: %s" (org-get-entry))
             ;; (setq prompt (org-get-entry))
             (save-excursion
               (org-back-to-heading t)
               (goto-char marker)
               ;; 获取 helm 选择的条目的 title ，不包含 todo 优先级 标签等信息
               (setq role (org-get-heading t t t))
               (setq helm-calude-current-role role)
               ;; 获取 helm 选择条目的内容
               ;; 例如：当选择的 prompt.org 中的 system 条目，就可以获取到 prompt 内容。
               (setq prompt (buffer-substring-no-properties
                             ;; start           ;; 包含heading
                             (line-beginning-position 2) ;; 不包含heading
                             (org-end-of-subtree t)))
               (message "获取prompt: %s" prompt)
               )
             )))
    ```
4.  helm-AI-prompt 基于 helm-rog-ql 限制搜索的范围，仅展示 prompt.org 文件中的 heading 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun helm-AI-prompt()
         (interactive)
         (helm-org-ql "~/hsg/iNotes/content-org/prompt/prompt.org"
                      :name "AI prompt")
         )
    ```

```text
helm-org-ql-AI-show-marker
```


## 基于 org-ql 定制 helm 查询 prompt {#基于-org-ql-定制-helm-查询-prompt}

helm-claude-role：基于 helm + org-ql-query 实现. 通过 query 语法定制 helm 数据源。 

显示 prompt.org 的一级heading 

1.  声明交互函数，直接调用 helm 接口展示搜索页面 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       ;; 基于 helm 直接封装
       (defun helm-claude-role ()
         (interactive)
         (helm
          :prompt (format " %s 切换角色为：" helm-calude-current-role)
          :sources helm-AI-prompt-source
          :buffer "*Helm AI prompt*"
          :full-frame nil
          )
         )
    ```
2.  使用 org-ql-query 读取 prompt 库文件，初始化数据源 
    
    通过 org-ql-query 屏蔽 system user 等级，限制 helm 显示的文本内容 
    
    弊端：不支持 tags todo 等高级搜索 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       ;;       为helm提供数据元数据
       ;; 通过 org-ql-query 屏蔽 system user 等级，限制 helm 显示的文本内容
       ;; 弊端：不支持 tags todo 等高级搜索
       (defvar helm-AI-prompt-source
         (helm-make-source "Org QL Views" 'helm-source-sync
           :candidates (lambda ()
                         ;; TODO 查询之后整理出列表格式，提供显示和action传值。
                         ;; 获取数据helm的数据源，通过不同方式整理成 helm支持的格式 list
                         (org-ql-query
                           :select '(list (org-get-heading)
                                     (org-get-heading t t t))
                           :from "~/hsg/iNotes/content-org/prompt/prompt.org"
                           :where '(children)))
           :action (list
                    ;; TODO 以这种格式添加选择之后的操作
                    ;; 1. 声明一个变量，传递给 go-translate 引擎
                    (cons "设置 prompt 名称" (lambda (x)
                                               (message "选择的heading:%s" (car x))
                                               (setq helm-calude-current-role (car x))
                                               ))
                    ))
         )
    ```


## 使用场景: 在 go-translate 中使用 AI prompt {#使用场景-在-go-translate-中使用-ai-prompt}

helm-claude-tranlate 使用方式 

1.  需要先设置 `helm-claude-current-role` 角色，默认：英翻中
2.  可以通过 helm 选择切换新角色，支持helm 的工具有： 
    1.  helm-org-ql : 新增了选择事件，在使用时，要确保条目来自prompt.org中，这样才能得到正确的 prompt，具体看 prompt.org 的结构
    2.  helm-AI-prompt：基于 helm-org-ql ,仅限制了在 prompt.org 文件中搜索,其他功能一样
    3.  helm-claude-role：基于 helm + org-ql-query 实现，通过query 语法 helm 数据源，显示 prompt.org 的一级heading

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
  (setq helm-calude-current-role "英翻中")
  (defvar helm-claude-translater
    (gts-translator
     :picker (lambda ()
               (cond ((equal helm-calude-current-role "英翻中")
                      (gts-noprompt-picker :texter (gts-current-or-selection-texter)))
                     ((equal helm-calude-current-role "内容总结")
                      (gts-paragraph-picker))
                     (t (gts-prompt-picker))))
     :engines (lambda ()
                (gts-claude-engine :auth-key slack-claude-bot-token
                                   :channel "D052Y13FSRY"
                                   :system  helm-calude-current-role)
                )
     :render (lambda ()
               (cond ((equal helm-calude-current-role "英翻中")
                      (gts-posframe-pop-render)
                      (t (gts-buffer-render))))
               )))

  (defun helm-claude-tranlate ()
    (interactive)
    (gts-translate helm-claude-translater))
```


## 参考代码片段 {#参考代码片段}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(cl-defun helm-org-ql-source (buffers-files &key (name "helm-org-ql"))
  "Return Helm source named NAME that searches BUFFERS-FILES with `helm-org-ql'."
  ;; Expansion of `helm-build-sync-source' macro.
  (helm-make-source name 'helm-source-sync
    :candidates (lambda ()

                  (let* ((query (org-ql--query-string-to-sexp helm-pattern))
                         (window-width (window-width (helm-window))))
                    (when query
                      (with-current-buffer (helm-buffer-get)
                        (setq helm-org-ql-buffers-files buffers-files))
                      (ignore-errors
                        ;; Ignore errors that might be caused by partially typed queries.
                        (org-ql-select buffers-files query
                          :action `(helm-org-ql--heading ,window-width))))))
    :match #'identity
    :fuzzy-match nil
    :multimatch nil
    :nohighlight t
    :volatile t
    :keymap helm-org-ql-map
    :action helm-org-ql-actions))



;;; Org capture templates
;;
;;
(defvar org-capture-templates)
(defun helm-source-org-capture-templates ()
  "Build source for org capture templates."
  (helm-build-sync-source "Org Capture Templates:"
    :candidates (cl-loop for template in org-capture-templates
                         collect (cons (nth 1 template) (nth 0 template)))
    :action '(("Do capture" . (lambda (template-shortcut)
                                (org-capture nil template-shortcut))))))

(defun helm-org-capture-templates ()
  "Preconfigured helm for org templates."
  (interactive)
  (helm :sources (helm-source-org-capture-templates)
        :truncate-lines helm-org-truncate-lines
        :buffer "*helm org capture templates*"))
```

