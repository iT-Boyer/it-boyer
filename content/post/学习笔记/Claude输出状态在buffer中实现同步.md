+++
title = "扩展 go-translate 渲染器支持 Claude Typing 输入状态"
description = "博客简介"
date = 2023-05-25T22:17:00+08:00
lastmod = 2023-05-28T18:39:59+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## AI需求 {#ai需求}

描述需求，投喂给 AI 给出建议，获取灵感，例如： 

```text
你是精通elisp语法，了解emacs的常规用法和习惯，请帮我实现下面的功能：

这是数组A：(list "你好，Typing" "你好，世界！Typing" "你好，世界！生活很美好，Typing" "你好，世界！生活很美好，要面对现实Typing")
写一个elisp函数：在当前buffer的光标位置，依次将数组A中的内容全部插入到光标的位置，要求每次插入的内容不能和已插入个的内容内容重复，次序要保持连贯。
插入的预期效果："你好，世界！世界!生活很美好，要面对现实"。
```


### Claude 灵感 {#claude-灵感}

这个函数会在当前光标的位置插入所有内容，不会出现重复。同时，如果已经插入了部分内容，新的内容会接在已有内容的后面。这里是实现该功能的Emacs Lisp代码: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun insert-without-duplicate-at-point (lst)
  "Insert elements of LIST into current buffer at point without duplicates."
  (message "开始-----")
  (let ((existing-content "")
        (point (point)))
    (message "插入开始,位置ddd: %s" point)
    (dolist (elem lst)
      (message "正在插入：%s" elem)
      (unless (string-match (regexp-quote elem) existing-content)
        (goto-char point)
        (insert elem)
        (setq point (point))
        (message "插入完成的位置: %s" point)
        (setq existing-content (buffer-substring point (point-max)))
        )
      )
    ))

(setq list '("你好,Typing" "你好,世界!Typing"
             "你好,世界!生活很美好,Typing"
             "你好,世界!生活很美好,要面对现实Typing"))
(insert-without-duplicate-at-point list)
```

你好,Typing你好,世界!Typing你好,世界!生活很美好,Typing你好,世界!生活很美好,要面对现实Typing 

这个函数 `insert-without-duplicate` 接受一个列表和缓冲区作为参数。它首先获取缓冲区的现有内容,然后迭代列表中的每个元素。对于每个元素,它使用 `string-match` 检查该元素是否已经存在于缓冲区中。如果不存在,则使用 insert 将元素插入缓冲区,并更新现有内容。 

我们将示例列表 list 传递给这个函数,将元素插入当前缓冲区。结果缓冲区的内容将是: 

你好,世界!世界!生活很美好,要面对现实 

这个函数确保列表的元素被连续插入缓冲区,同时不包含任何重复项,与要求一致。 


### 编写伪代码 {#编写伪代码}

在获取到最新内容时，使用正则匹配已经显示过内容，截取出新正的内容。然后插入到 buffer 的最后正则的规划， `^.*关键字.\s*` 关键字存在两种状态： 

1.  必须是全局变量
2.  第一次本地没有内容，不需要截取，需要将 Type 字段清理掉，然后复制给关键字，用于下一次正则匹配。
3.  每次在截取完之后，将新增的内容复制给关键字。 依次类推。

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq gts-render-regkey "")
(setq gts-render-lastpoint (point))

;; 正在输入中时，继续下一次请求，在请求过程中，可以先解析一步内容展示
(if-let* ((typing (string-suffix-p "_Typing…_" parsed))
          ;; 每次先处理掉 Typing 后缀
          (let ((point (point))
                (regkey gts-render-regkey)
                (content (replace-regexp-in-string "\n\n_Typing…_" "" parsed)))
            ;; 第一次先记录
            (if (= regkey "")
                (setq regkey content)
              (progn
                (setq regkey (content (replace-regexp-in-string (format "^.*%s" regkey) "" content)))
                )
              )
            ;;将内容插入当前位置
            (goto-char gts-render-lastpoint)
            (insert regkey)
            ;;更新到全局中存储: 新位置和正则关键字
            (setq gts-render-lastpoint (point))
            (setq gts-render-regkey regkey)
            )
          )
    (progn
      (message "Claude正在输入...")
      )
  ;; 否则，即为输入完成：展示
  (insert "\n" parsed "\n"))
```


## 在 go-translate 实现 render 渲染器 {#在-go-translate-实现-render-渲染器}


### 集成到项目正则实现 {#集成到项目正则实现}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq gts-render-regkey "")
  (setq gts-render-lastpoint (point))
  (cl-defmethod gts-out ((_ gts-current-buffer-render) task)
    (deactivate-mark)
    (with-slots (err parsed) task
      (if err
          (user-error "%s" err)

        ;; 根据解析结果，判断状态：
        ;; 正在输入中时，继续下一次请求，在请求过程中，可以先解析一步内容展示
        (if-let* ((typing (string-suffix-p "_Typing…_" parsed))
                  ;; (result (replace-regexp-in-string "\n*\s*_Typing…_.*" "" parsed))
                  ;;
                  )
            (progn
              (message "Claude正在输入...")
              ;; 每次先处理掉 Typing 后缀
              (let ((point (point))
                    (regkey gts-render-regkey)
                    (content (replace-regexp-in-string "\n\\{0,\\}_Typing…_" "" parsed)))
                ;; 第一次先记录
                (if (string= regkey "")
                    (setq regkey content)
                  (progn
                    (message "正则匹配：%s" regkey)
                    (setq regkey (replace-regexp-in-string (format "^.*%s" regkey) "" content))
                    (message "匹配匹配后的内容：%s" regkey)
                    )
                  )
                ;;将内容插入当前位置
                (goto-char (point))
                (insert regkey)
                ;;更新到全局中存储: 新位置和正则关键字
                ;; (setq gts-render-lastpoint (point))
                (setq gts-render-regkey regkey)
                ))
          ;; 否则，即为输入完成：展示
          ;; (insert "\n" parsed "\n")
          )
        )))
```


### 在项目中使用 substring 截取方式实现 {#在项目中使用-substring-截取方式实现}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq gts-render-stored "")
  (cl-defmethod gts-out ((_ gts-current-buffer-render) task)
    (deactivate-mark)
    (with-slots (err parsed) task
      (if err
          (user-error "%s" err)

        ;; 根据解析结果，判断状态：
        ;; 正在输入中时，继续下一次请求，在请求过程中，可以先解析一步内容展示
        (if-let* ((point (point))
                  (inserted gts-render-stored)
                  (typing (string-suffix-p "_Typing…_" parsed))
                  ;; (result (replace-regexp-in-string "\n*\s*_Typing…_.*" "" parsed))
                  ;;
                  )
            (progn
              (message "Claude正在输入...")
              ;; 每次先处理掉 Typing 后缀
              (let ((content (replace-regexp-in-string "\n\\{0,\\}_Typing…_" "" parsed)))
                ;;将内容插入当前位置
                (goto-char (point))
                (setq inserting (substring content (length inserted) (length content)))
                (insert inserting)
                ;;更新到全局中存储: 新位置和正则关键字
                (setq gts-render-stored content)
                ))
          ;; 否则，即为输入完成：展示
          (goto-char (point))
          (setq inserting (substring parsed (length gts-render-stored) (length parsed)))
          (insert inserting "\n")
          )
        )))
```


## 通过模拟数据梳理处理规范 {#通过模拟数据梳理处理规范}


### 使用正则匹配 {#使用正则匹配}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((result "")
      (new "")
      (content ""))
  (setq new "在Emacs Lisp中,循环允许你重复执行一系列表达式。主要有以下两种类型:\n\n1. while -\n\n_Typing…_")
  (setq content "在Emacs Lisp中,循环允许你重复执行一系列表达式。主要有以下两种类型:\n\n1. while - 通过一个condition循环:\n\n```elisp\n(while condition \n  (expression1)\n  (expression2) \n  ...) \n```\n\n这会在condition\n\n_Typing…_")
  ;; (setq new "_Typing…_")
  (setq regkey (replace-regexp-in-string "\n\\{0,2\\}_Typing…_" "" new))
  (message "内容：%s" content)
  (setq result (replace-regexp-in-string (format "^.*%s" regkey) "" content))
  (message "结果数据内容：%s" content)
  )
```


### 使用 replace-string 方式处理字符串 {#使用-replace-string-方式处理字符串}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; 直接替换的方式
  (setq new "你好")
  (setq content "你好,世界！")
  (replace-string (format "%s" new) "" content)
  (message "内容：%s" new)
  (message "截取之后内容：%s" content)
```

```text
截取之后内容：,世界！
```


### substring 截取字符串 {#substring-截取字符串}

`(substring string start &optional end)` 

其中， `string` 是你要截取的字符串， `start` 是截取的起始位置， `end` 是截取的结束位置。如果省略 `end` 参数，那么将截取从 `start` 位置开始到字符串结尾的所有字符。 

以下是一个例子： 

```emacs-lisp
(setq str "Hello, world!")
(setq sub-str (substring str 0 5)) ; 截取 "Hello"
```

以上代码中，我先定义了一个字符串 `str=。然后，我使用 =substring` 函数截取 `str` 的前五个字符，并将其赋值给 `sub-str` 变量。注意，字符串的索引是从 0 开始的。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((result "")
      (new "")
      (content ""))
  (setq new "在Emacs Lisp中,循环允许你重复执行一系列表达式。主要有以下两种类型:\n\n1. while -\n\n_Typing…_")
  (setq content "在Emacs Lisp中,循环允许你重复执行一系列表达式。主要有以下两种类型:\n\n1. while - 通过一个condition循环:\n\n```elisp\n(while condition \n  (expression1)\n  (expression2) \n  ...) \n```\n\n这会在condition\n\n_Typing…_")
  ;; (setq new "_Typing…_")
  (setq regkey (replace-regexp-in-string "\n\\{0,2\\}_Typing…_" "" new))
  (message "regkey长度：%s" (length regkey))
  (setq result (substring content (length regkey) (length content)))
  (message "截取之后的：%s" result)
  )
```

````text
截取之后的： 通过一个condition循环:

```elisp
(while condition
  (expression1)
  (expression2)
  ...)
```

这会在condition

_Typing…_
````

