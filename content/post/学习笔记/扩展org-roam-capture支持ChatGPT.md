+++
title = "扩展 org-roam-capture 模板支持ChatGPT"
description = "博客简介"
date = 2023-05-17T11:52:00+08:00
lastmod = 2023-05-20T09:41:17+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

通过扩展org-roam-capture 模板完善 emacs 工作流: 

收集灵感 --&gt; 配置 chatGPT prompt --&gt; 发送到 ChatGPT --&gt; 获得建议和处理内容 --&gt; 根据反馈，分析灵感内容，是否进行下一步安排。 

本文主要包括以下功能： 

1.  定制 AI capture 模板，支持角色属性和 AI 模板标签，添加 AI 分组。
2.  利用 org-capture-template 的特性动态加载预设的 ChatGPT prompt 模板。
3.  添加 hook 函数，在保存 capture 之后，和 ChatGPT 交互，并将处理的内容追加到结果中。


## 先定义一个 capture 模板 {#先定义一个-capture-模板}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(add-to-list 'org-roam-capture-templates
             '("al" "inbox:捕获灵感"
               entry
               "* ${title}\t:灵感:\n prompt:\n %(add-prompt-from-json)\n"
               :if-new (file "Orgzly/inbox.org")
               :empty-lines 1
               :jump-to-captured 1
               :role "写作"
               :ai t
               ))
```

1.  动态添加 role prompt : 使用 `%(方法)` 从 json 中获取 prompt 添加到 entry 中。
2.  `:ai t` : 设定为 ai 模板，主要在 hook 方法中使用，可以针对这类模板，做特殊处理。
3.  `:role "写作"` : 添加 prompt 角色属性，主要在动态添加 prompt 时，用来匹配 json 数据。以下是一个示例 elisp 函数，可以使用它向 org-capture-templates 列表中添加多个模板：

开启 AI 的模板分组，在使用 capture 便于管理 

```emacs-lisp
;; 使用 =append= 函数将新模板组添加到 =org-capture-templates= 列表中。
(defun add-org-capture-templates (new-templates)
  "Add NEW-TEMPLATES to the `org-capture-templates' list."
  (setq org-capture-templates (append org-capture-templates new-templates)))
;; 向 org-capture-templates 列表中添加多个模板
(add-org-capture-templates 'org-roam-capture-templates
                           '(("a" "AI cupture")
                             ("al" "inbox:捕获灵感"
                              entry
                              "* ${title}\t:灵感:\n prompt:\n %(add-prompt-from-json)%?"
                              ;; (file "~/.doom.d/templates/org-mode/AI/写作助手.org")
                              :if-new (file "Orgzly/inbox.org")
                              :empty-lines 1
                              :jump-to-captured 1
                              :role "写作"
                              :ai t)
                             ))
```


## 从加载 json 中预设的 prompt {#从加载-json-中预设的-prompt}

```elisp
(defun add-system-prompt-from-json ()
  (let* ((prompt)
         (role (plist-get org-capture-plist :role))
         (desc (plist-get org-capture-plist :description))
         (json-object-type 'plist)
         (json-array-type 'list)
         (json (json-read-file "~/hsg/iNotes/content/prompt/roles.json")))
    (message "获取角色：%s 描述：%s" role desc)
    (dolist (object (plist-get json :roles))
      (when (string= (plist-get object :title) "佛祖")
        (setq prompt (plist-get object :descn))
        ))
    prompt))
```

1.  解析 json 文件中的对象列表
2.  匹配 `:role` 指定的角色，返回 角色的 prompt 。

问题：这种方式无法在 capture 过程中使用 `^%{主题}` 占位符模板，职能使用确定的 prompt 模板内容。 

解决办法文件模板设置 capture 模板，这样需要为每个 AI 模板编写文件模板。 

下面举例说明： 

先设置 AI capture 模板，这样就不需要 `:role "写作"` 属性 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(add-to-list 'org-roam-capture-templates
             '("at" "测试文件模板"
               entry
               (file "~/.doom.d/templates/org-mode/AI/写作助手.org")
               :if-new (file "Orgzly/inbox.org")
               ;; :role "写作"
               :ai t
               ))
```

`写作助手.org` 文件模板样例： 

```org { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
* TODO ${title}
我写关于%^{主题}。

请给我一些开始的要点:

- 相似的主题是什么?
- 主题的基础是什么?
- 为什么创建“%^{主题}”主题?
- 它解决了什么问题?
```


## 借助 gptel 和 openAI 通信 {#借助-gptel-和-openai-通信}

在 capture 完成时，执行 AI 方法 

1.  自定义 hook 方法：获取 capture 相关属性 hook 方法对所有 capture 模板都生效，即当 caputre 完成时，都会执行该方法，需要在 hook 方法中添加过滤条件，实现自定义需求。之前已经在 capture 模板自定义属性： `:ai t` , 当 `ai` 属性为 t 时，执行下面的操作。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun gptel-capture-hook ()
         (if org-note-abort
             (message "带有键 %s 和描述 %s 的模板终止" key desc)
           ;;调用 gptel 发送给 AI
           (if (org-capture-get :ai)
               (let ((key  (plist-get org-capture-plist :key))
                     (desc (plist-get org-capture-plist :description))
                     (org-str (plist-get org-capture-plist :template))
                     (role (plist-get org-capture-plist :role))
                     )
                 ;; 通过解析字符串获取用户模板内容
                 ;; (setq content (content-from-org-element org-str))
                 ;; 从json文件中获取system prompt
                 ;; (setq system (add-system-prompt-from-json))
    
                 ;; 第一步获取 capture 字符串中的AI tag
                 ;; (setq aitag (get-element-tag-by-ql org-str "AI"))
                 ;; 在capture 时，竟然拿不到 tag 值
                 (setq aitag role)
                 ;; 第二步获取 使用org-ql 从 org 文件中获取system
                 ;; aitag 作为 title 获取 prompt
                 (setq promptfile "~/hsg/iNotes/content-org/prompt/prompt.org")
                 (setq system (get-element-prompt-by-ql promptfile aitag 1))
                 (setq user (get-element-prompt-by-ql promptfile aitag 0))
                 (message "结果=-%s \n -%s" system user)
                 (gptel-capture-request system org-str)
                 )
             )))
    
       (defun get-element-tag-by-ql (org-str tagregex)
    
         (let (result)
           (with-current-buffer (get-buffer-create "*gpteltagtemp*")
             (erase-buffer)
             (insert org-str)
             (org-mode)
             (setq tags (org-ql-query
                          :select '(org-entry-get (point) "TAGS")
                          :from (current-buffer)
                          :where '(and
                                   (tags* (format "^%s.*$" tagregex))
                                   (not (parent))
                                   )
                          ))
             (message "数组：%s" tags)
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
    
       ;; ptype 1 :system 其他：user
       (defun get-element-prompt-by-ql (file title ptype)
         (let (prompt)
           (if (= ptype 1)
               (setq prompt (org-ql-query
                              :select '(org-get-entry)
                              :from file
                              :where '(and
                                       (ancestors (heading title))
                                       (heading "system")
                                       )))
             (setq prompt (org-ql-select  file
                            '(and
                              (ancestors (heading title))
                              (heading "user")
                              )
                            :action '(org-get-entry)
                            ))
             )
           ))
       ;; 解析org-str，获取指定部分内容
       (defun content-from-org-element (org-str &optional headline)
         (with-temp-buffer
           (erase-buffer)
           (insert org-str)
           (org-mode)
           (setq content "")
           (org-element-map (org-element-parse-buffer) 'paragraph
             (lambda (x)
               ;;把获取到的段落，格式为text
               (setq item (org-element-interpret-data x))
               ;;拼接循环到的每个段落，用\n分割
               (setq content (format "%s\n%s" content item))
               ))
           content))
    ```

2.  监听 hook 方法： `org-capture-after-finalize-hook` 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (add-hook 'org-capture-after-finalize-hook 'gptel-capture-hook)
    ```
    在包 AI cupture 事项保存到文件中之后，将自动调用 `gptel-capture-hook` 函数。 
    
    参考： 
    
    [Template elements (The Org Manual)](https://orgmode.org/manual/Template-elements.html) 
    
    [org mode - creating an org-capture template - Emacs Stack Exchange](https://emacs.stackexchange.com/questions/24691/creating-an-org-capture-template)

<!--listend-->

1.  通过 gptel 和 openAI 交互 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun gptel-capture-request (sytem prompt)
         (when (string= prompt "") (user-error "A prompt is required."))
         (message "开始网络 %s \n %s" system prompt)
         (setq gptel--system-message sytem)
         (gptel-request
          prompt
          :callback
          (lambda (response info)
            (if (not response)
                (message "gptel-capture 错误: %s" (plist-get info :status))
              (with-current-buffer (plist-get org-capture-plist :buffer)
                (goto-char (point-max))
                (unless (bolp)
                  (insert "\n"))
                (insert "**  测试 \n" response "\n")
                )
              ))))
    ```
    参考：[GitHub - karthink/gptel: A no-frills ChatGPT client for Emacs](https://github.com/karthink/gptel)

