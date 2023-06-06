+++
title = "介绍org-ql函数和宏的用法"
description = "博客简介"
date = 2023-05-20T10:59:00+08:00
lastmod = 2023-05-20T11:58:22+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## Agenda-like views {#agenda-like-views}


### Function: `org-ql-block` {#function-org-ql-block}

要用作自定义 agenda 议程块，请键入 `org-agenda-custom-commands` 。例如，您可以像这样定义一个自定义系列命令，它将在单个缓冲区中列出 Tag 为 `emacs` ,所有优先级 A 项，其中 todo 状态为 `SOMEDAY` ，后面跟着标准 agenda 议程视图: 

```elisp
  (setq org-agenda-custom-commands
        '(("ces" "Custom: Agenda and Emacs SOMEDAY [#A] items"
           ((org-ql-block '(and (todo "SOMEDAY")
                                (tags "Emacs")
                                (priority "A"))
                          ((org-ql-block-header "SOMEDAY :Emacs: High-priority")))
            (agenda)))))
```

这相当于像这样的 `tags-todo` 搜索: 

```elisp
  (setq org-agenda-custom-commands
        '(("ces" "Custom: Agenda and Emacs SOMEDAY [#A] items"
           ((tags-todo "PRIORITY=\"A\"+Emacs/!SOMEDAY")
            (agenda)))))
```

但是， `org-ql-block` 版本的运行时间大约是原来的1/5。 

变量 `org-ql-block-header` 可以绑定到一个字符串作为 header，否则会自动生成 header。 


## Listing / acting-on results {#listing-acting-on-results}


### Caching {#caching}

Org 使用逐缓冲区缓存来加速后续搜索。 

它以查询表达式和匹配操作为关键字，这意味着，对于相同缓冲区中的相同查询和相同匹配操作，如果自上次运行查询以来缓冲区未被修改，则将返回缓存的匹配操作结果，并且不会在该缓冲区中再次求值查询。 

因此，由于在调用以下函数时，查询表达式和匹配操作都不能保证被求值，因此它们应该没有副作用。或者，如果需要副作用，则缓存应该无效(例如，通过增加缓冲区的修改标记，或通过使用尚未缓存的查询表达式或匹配操作)。 

_注:未来的改进将允许更容易地禁用或清除缓存._ 


### Function: `org-ql-select` {#function-org-ql-select}

_参数:_ `(buffers-or-files query &key action narrow sort)` 返回值： `QUERY` 查询条件 在 `BUFFERS-OR-FILES` buffer 或文件中匹配到的条目. 

`BUFFERS-OR-FILES` 是一个或多个文件或缓冲区。 

`QUERY` 是一个 `org-ql` 查询语句 sexp (引号，因为这是一个函数)。 

`ACTION` 是一个函数，它在每个匹配的目标题开头的位置上调用。可能是: 

-   `element` or nil: 相当于 `org-element-headline-parser`.

-   `element-with-markers`: 相当于调用 `org-element-headline-parser`,用加 `org-ql--add-markers` 上标记.  适合使用  `org-ql-agenda--format-element` 进行格式化, 允许插入到 Org Agenda-like buffer 中.

-   A sexp, 它将被字节编译为lambda函数。

-   A function symbol.

如果 `NARROW` 为非空值，则不会对缓冲区进行展开(默认是展开并搜索整个缓冲区)。 `SORT` 为 nil，在这种情况下，条目不作排序;或者一个或一组定义的 `org-ql` 排序方法(`date`, `deadline`, `scheduled`, `closed`, `todo`, `priority`, or `random`); 或者一个用户定义的比较器函数，它接受两个项作为参数并返回 nil 或 non-nil。 

Examples: 

```elisp
  ;; Return list of to-do headings in inbox file with tags and to-do keywords:
  (org-ql-select "~/org/inbox.org"
    '(todo)
    :action #'org-get-heading)
  ;; => ("TODO Practice leaping tall buildings in a single bound  :personal:" ...)

  ;; Without tags and to-do keywords:
  (org-ql-select "~/org/inbox.org"
    '(todo)
    :action '(org-get-heading t t))
  ;; => ("Practice leaping tall buildings in a single bound" ...)

  ;; Return WAITING heading elements in agenda files:
  (org-ql-select (org-agenda-files)
    '(todo "WAITING")
    :action 'element)
  ;; => ((headline (:raw-value "Visit the moon" ...) ...) ...)

  ;; Since `element' is the default for ACTION, it may be omitted:
  (org-ql-select (org-agenda-files)
    '(todo "WAITING"))
  ;; => ((headline (:raw-value "Visit the moon" ...) ...) ...)
```


### Function: `org-ql-query` {#function-org-ql-query}

_参数:_ `(&key (select 'element-with-markers) from where order-by narrow)` 

Like `org-ql-select`, 但是参数的命名更像 `SQL` 查询. 

-   `SELECT` 作用等价于 `org-ql-select` 的 `ACTION` 功能.
-   `FROM` 作用等价于 `org-ql-select` 的 `BUFFERS-OR-FILES` 的功能.
-   `WHERE` 作用等价于 `org-ql-select` 的 `QUERY` 功能.
-   `ORDER-BY` 作用等价于 `org-ql-select` 执行 `SORT` 功能.
-   `NARROW` 作用等价于 `org-ql-select` 执行 `NARROW` 功能.

Examples: 

```elisp
  ;; Return list of to-do headings in inbox file with tags and to-do keywords:
  (org-ql-query
    :select #'org-get-heading
    :from "~/org/inbox.org"
    :where '(todo))
  ;; => ("TODO Practice leaping tall buildings in a single bound  :personal:" ...)

  ;; Without tags and to-do keywords:
  (org-ql-query
    :select '(org-get-heading t t)
    :from "~/org/inbox.org"
    :where '(todo))
  ;; => ("Practice leaping tall buildings in a single bound" ...)

  ;; Return WAITING heading elements in agenda files:
  (org-ql-query
    :select 'element
    :from (org-agenda-files)
    :where '(todo "WAITING"))
  ;; => ((headline (:raw-value "Visit the moon" ...) ...) ...)

  ;; Since `element' is the default for SELECT, it may be omitted:
  (org-ql-query
    :from (org-agenda-files)
    :where '(todo "WAITING"))
  ;; => ((headline (:raw-value "Visit the moon" ...) ...) ...)
```


### Macro: `org-ql` (deprecated) {#macro-org-ql--deprecated}

_参数:_ `(buffers-or-files query &key sort narrow markers action)` `org-ql` 扩展为对 `org-ql-select` 的调用，带有相同的参数。为方便起见，参数应该不加引号。 

_Note: 此宏已弃用，并将在 v0.7 移除._ 


## Custom predicates {#custom-predicates}


### <span class="org-todo todo TODO">TODO</span> Macro: `org-ql-defpred` {#macro-org-ql-defpred}

_参数:_ `(name args docstring &key body preambles normalizers)` 

1.  定义一个 `org-ql` 选择器谓词，命名为 `org-ql——predicate-NAME` 。 
    
    `NAME` 可以是一个符号或一个符号列表:如果是一个列表，第一个被用作 `NAME` ，其余的是别名。 `A` 函数仅为 `NAME` 创建，而不是为别名创建，因此应该使用规范化器在查询中用 `NAME` 替换别名(继续阅读)。
2.  `ARGS` 是一个 `cl-defun` 风格的参数列表。 `DOCSTRING` 是函数的注释内容。
3.  `BODY` 是谓词的主体。 
    
    它将在一个 org 标题的开头进行匹配，如果标题的条目是匹配的，它应该返回 non-nil。
4.  `PREAMBLES` 和 `NORMALIZERS` 是与 `org-ql` 查询示例匹配的 `pcase` 表单列表。 
    
    它们在函数 `org-ql——query-preamble` 和 `org-ql——normalize-query` 的定义中拼接成 `pcase` 形式，见。这些函数在展开宏时被重新定义，除非变量 `org-ql-defpred-defer` 为 non-nil，在这种情况下，应该在调用 `org-ql--define-query-preamble-fn` 和 `org-ql--define-normalize-query-fn` 定义谓词后手动重新定义这些函数.
5.  `NORMALIZERS` 用于将查询表达式规范化为标准形式。 
    
    例如，当谓词有别名时，应该使用  normalizer 规范化器将别名替换为谓词名称。此外，谓词参数可以采用更优化的形式，以便谓词在查询时要做的工作更少。注意:在查询完全规范化之前，normalizer 规范化器会反复应用于查询，因此应该仔细编写规范化器以避免无限循环。

`PREAMBLES` 引用正则表达式，可用于直接在缓冲区中搜索潜在的匹配项，而不是在每个标题上测试谓词体。(命名是困难的) 在 `PREAMBLES` 中的每个 `pcase` 形式里， `pcase` 表达式(不是模式)应该是一个具有以下键的列表，每个键的值应该是一个表达式，可以引用模式中绑定的变量: 

`:regexp` 直接搜索到潜在匹配项的正则表达式。 

`:case-fold` 绑定 `case-fold-search` 的正则表达式搜索 

`:query` 应该替换查询表达式的表达式，或者不应该更改的 `query` (例如，如果 regexp 不足以确定标题是否匹配，在这种情况下，需要在标题上测试谓词的主体)。如果 regexp 保证匹配，这可能只是 `t` ，使查询表达式无需做任何工作，从而提高了性能。 

为方便起见，在 `pcase` 模式中，符号 `predicate-names` 是一种特殊的形式，它被替换为匹配谓词的任何名称和别名的模式。 

例如，如果 `NAME` 是: `(heading h)` 

然后,如果 `NORMALIZERS` 是: ``((`(,predicate-names . ,args) `(heading ,@args)))`` 

`(heading h)` 将解释为: ``((`(,(or 'heading 'h) . ,args) `(heading ,@args)))``

