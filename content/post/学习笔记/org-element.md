+++
title = "org-element.el API 联调文档"
description = "博客简介"
date = 2023-05-19T00:21:00+08:00
lastmod = 2023-05-19T00:31:54+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

主要目的是使用 API 解析字符串中的 org-mode 数据。获取对应的节点内容等。 

测试方法：使用 ob-babel 功能，支持共享数据和环境变量，可以传值等特点。 


## 初始化数据源 {#初始化数据源}

<a id="code-snippet--data"></a>
```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq org-string "* Hello world\n:PROPERTIES:\n:OWNER: Dav\n:ID: 123\n:END:\n\nThis is the body of first-level headline.\n\n** Subheadline\n\nTest text.")
(with-current-buffer (get-buffer-create "*org-string*")
        (insert org-string)
      )
```


## APi 测试用例 {#api-测试用例}


### 获取所有属性名 {#获取所有属性名}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (let ((x (org-buffer-property-keys)))
    (with-output-to-temp-buffer "*xah temp out*"
      (print x)))
  )
```

|    |       |
|----|-------|
| ID | OWNER |


### 获取当前 headline {#获取当前-headline}

```elisp

(with-current-buffer "*org-string*"
  (org-mode)
  ;; 提取当前标题的组成部分。
  (let ((x (org-heading-components)))
    (with-output-to-temp-buffer "*xah temp out*"
      (print x)))
  )
```

|   |   |     |     |             |     |
|---|---|-----|-----|-------------|-----|
| 2 | 2 | nil | nil | Subheadline | nil |


### 获取所有 headline {#获取所有-headline}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (let (($headings nil))
    (org-map-entries
     (lambda ()
       (push (org-heading-components) $headings)))
    (with-output-to-temp-buffer "*xah temp out*"
      (print $headings)))
  )
```

| 2 | 2 | nil | nil | Subheadline | nil |
|---|---|-----|-----|-------------|-----|
| 1 | 1 | nil | nil | Hello world | nil |


## org-element.el 库测试用例 {#org-element-dot-el-库测试用例}


### `org-data` 的数据结构 {#org-data-的数据结构}

`(org-data nil <e1> <e2> <e3> … )` 

每个 `<e1>` 都有 `<elm_form>`: 

`(<elm_type_name> <plist> <x>)` 

其中 `<x>` 代表 0 或 1 个 `<elm_form>` 。(可能更多，但到目前为止我只看到0或1个。) 

`<elm_type_name>` 有两中类型 `org-element-all-elements` ， `org-element-all-objects` 。例如: `org-element-all-elements` 包含。 `section` `keyword` `headline` `property-drawer` `paragraph` `clock` `comment` `comment-block` `diary-sexp` `item` `node-property` `src-block` `table` `table-row` `verse-block` 

参考：[Emacs Lisp: Parse Org Mode](http://xahlee.info/emacs/emacs/elisp_parse_org_mode.html) 


### 获取 buffer 的 org-data {#获取-buffer-的-org-data}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (let ((x (org-element-parse-buffer)))
    (with-output-to-temp-buffer "*xah temp out*"
      (print x)))
  )
```

|          |                                                                                                                                                                                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| org-data | (:begin 1 :contents-begin 1 :contents-end 125 :end 125 :robust-begin 3 :robust-end 123 :post-blank 0 :post-affiliated 1 :path nil :mode org-data :CATEGORY nil :granularity headline) | (headline (:raw-value Hello world :begin 1 :end 125 :pre-blank 0 :contents-begin 15 :contents-end 125 :robust-begin 54 :robust-end 123 :level 1 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 1 :OWNER Dav :ID 123 :title Hello world :mode first-section :granularity headline :cached t :parent (org-data (:begin 1 :contents-begin 1 :contents-end 125 :end 125 :robust-begin 3 :robust-end 123 :post-blank 0 :post-affiliated 1 :path nil :mode org-data :CATEGORY nil :granularity headline) #0)) (headline (:raw-value Subheadline :begin 99 :end 125 :pre-blank 1 :contents-begin 115 :contents-end 125 :robust-begin 117 :robust-end 123 :level 2 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 99 :title Subheadline :mode section :granularity headline :parent #0))) |


### 获取 org-element-at-point {#获取-org-element-at-point}

返回有关光标位置的元素的列表数据 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (let ((x (org-element-at-point)))
    (with-output-to-temp-buffer "*xah temp out*"
      (print x)))
  )
```

|               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| node-property | (:key OWNER :value Dav :begin 28 :end 40 :post-blank 0 :post-affiliated 28 :mode node-property :granularity element :cached t :parent (property-drawer (:begin 15 :end 56 :contents-begin 28 :contents-end 49 :post-blank 1 :post-affiliated 15 :mode planning :granularity element :cached t :parent (section (:begin 15 :end 99 :contents-begin 15 :contents-end 98 :robust-begin 15 :robust-end 96 :post-blank 1 :post-affiliated 15 :mode section :granularity element :cached t :parent (headline (:raw-value Hello world :begin 1 :end 125 :pre-blank 0 :contents-begin 15 :contents-end 125 :robust-begin 54 :robust-end 123 :level 1 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 1 :OWNER Dav :ID 123 :title Hello world :mode first-section :granularity element :cached t :parent (org-data (:begin 1 :contents-begin 1 :contents-end 125 :end 125 :robust-begin 3 :robust-end 123 :post-blank 0 :post-affiliated 1 :path nil :mode org-data :CATEGORY nil :cached t))))))))) |


#### 获取 org-element-context {#获取-org-element-context}

返回点周围最小的元素或对象。返回值是一个类似于(TYPE PROPS)的列表，其中 

1.  `TYPE` 是元素或对象的 `org-element-type` 类型
2.  `PROPS` 是与其关联的属性 `org-element-property` 列表。

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (let ((x (org-element-context)))
    (with-output-to-temp-buffer "*xah temp out*"
      (print x)))
  )
```

|          |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| headline | (:raw-value Subheadline :begin 106 :end 132 :pre-blank 1 :contents-begin 122 :contents-end 132 :robust-begin 124 :robust-end 130 :level 2 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 106 :title Subheadline :mode nil :granularity element :cached t :parent (headline (:raw-value Hello world :begin 1 :end 132 :pre-blank 0 :contents-begin 15 :contents-end 132 :robust-begin 61 :robust-end 130 :level 1 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 1 :OWNER Dav :ID 123 :title Hello world :mode first-section :granularity element :cached t :parent (org-data (:begin 1 :contents-begin 1 :contents-end 132 :end 132 :robust-begin 3 :robust-end 130 :post-blank 0 :post-affiliated 1 :path nil :mode org-data :CATEGORY nil :cached t))))) |


#### 获取当前位置的元素类型 {#获取当前位置的元素类型}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (print (org-element-type (org-element-context))
           )
```


#### 获取当前位置的元素属性 {#获取当前位置的元素属性}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (print (org-element-property :ID  (org-element-at-point))
           )
```


### 解析 org-element-contents {#解析-org-element-contents}

返回给定元素或对象中所有元素或对象的有序列表(按缓冲区位置)。 

获取headline 的值 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (org-element-map (org-element-parse-buffer) 'headline
      (lambda (x)
        (princ (org-element-property :raw-value x))
        (terpri))))
  )
```

|   |   |
|---|---|
| t | t |


### 获取 headline ID {#获取-headline-id}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (org-element-map (org-element-parse-buffer)  'headline
      (lambda (x)
        (princ x)
        (let ((myid (org-element-property :ID x)))
          (when myid
            (princ "heading: " )
            (princ (org-element-property :raw-value x))
            (terpri )
            (princ "ID value is: " )
            (princ myid ))))))
  )
```

|     |
|-----|
| 123 |


### 获取 section {#获取-section}

is the body of 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (org-element-map (org-element-parse-buffer)  'section
      (lambda (x)
        (princ (org-element-contents x))
        )))
  )
```

|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| (property-drawer (:begin 15 :end 63 :contents-begin 28 :contents-end 56 :post-blank 1 :post-affiliated 15 :mode planning :granularity nil :parent (section (:begin 15 :end 133 :contents-begin 15 :contents-end 131 :robust-begin 15 :robust-end 129 :post-blank 2 :post-affiliated 15 :mode section :granularity nil :parent (headline (:raw-value Hello world :begin 1 :end 163 :pre-blank 0 :contents-begin 15 :contents-end 163 :robust-begin 61 :robust-end 161 :level 1 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 1 :OWNER Dav :ID 123 :title (Hello world) :mode first-section :granularity nil :parent (org-data (:begin 1 :contents-begin 1 :contents-end 163 :end 163 :robust-begin 3 :robust-end 161 :post-blank 0 :post-affiliated 1 :path nil :mode org-data :CATEGORY nil :granularity nil) #4)) #2 (headline (:raw-value Subheadline :begin 133 :end 163 :pre-blank 1 :contents-begin 149 :contents-end 163 :robust-begin 151 :robust-end 161 :level 2 :priority nil :tags nil :todo-keyword nil :todo-type nil :post-blank 0 :footnote-section-p nil :archivedp nil :commentedp nil :post-affiliated 133 :title (Subheadline) :mode nil :granularity nil :parent #4) (section (:begin 149 :end 163 :contents-begin 149 :contents-end 163 :robust-begin 149 :robust-end 161 :post-blank 0 :post-affiliated 149 :mode section :granularity nil :parent #5) (paragraph (:begin 149 :end 163 :contents-begin 149 :contents-end 163 :post-blank 0 :post-affiliated 149 :mode planning :granularity nil :parent #6) Test text   . |


### 获取 paragraph 和转文本的方法测试 {#获取-paragraph-和转文本的方法测试}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (org-element-map (org-element-parse-buffer)  'paragraph
      (lambda (x)
        (princ (org-element-contents x))
        (let ((content (org-element-contents x)))
          ;; (princ content)
          (princ (org-element-interpret-data x))
          )
        )))
  )
```

| This is the body of first-level headline. |
|-------------------------------------------|
| Test text.                                |


### 应用场景和测试数组处理 {#应用场景和测试数组处理}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(with-current-buffer "*org-string*"
  (org-mode)
  (with-output-to-temp-buffer "*xah temp out*"
    (setq itemstr "")
    (setq items '())
    (org-element-map (org-element-parse-buffer)  'paragraph
      (lambda (x)
        (setq item1 (org-element-interpret-data x))
        (setq item2 (org-element-contents x))
        (princ "原始数据：")
        (princ item1)
        (princ "\n")
        ;; (set itemstr (concat itemstr "\n" item1))
        (setq itemstr (format "%s\n%s" itemstr item1))
        (princ "结果数据：")
        (princ itemstr)
        (princ "\n")
        (add-to-list 'items item)
        ))
    (setq items (reverse items))
    (princ "最终：")
    (princ itemstr)
    )
  )
```

```text

This is the body of first-level headline.

tetanic

test2

tewet

Test text   .
```

