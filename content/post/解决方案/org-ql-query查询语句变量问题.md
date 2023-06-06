+++
title = "org-ql 查询语句如何解析变量值"
description = "博客简介"
date = 2023-05-28T23:42:00+08:00
lastmod = 2023-05-28T23:55:37+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

再使用 org-ql select 查询语句时，经常遇到通过变量来确定查询条件的情况，当直接将使用 format 等方法，将变量格式化为语句时，真正的查询条件并不执行， org-ql 会先通过 `org-ql--normalize-query` 方法处理： 

下面通过两种方式，验证在org-ql解析查询语句，最终得到的搜索语句 

1.  转义字面量查询条件 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (setq query (org-ql--normalize-query '(and
                                              (heading "system")
                                              (ancestors (heading "你好"))
                                              )))
       (message "语句：%s " query)
    ```
    
    ```text
    语句：(and (ancestors #[nil \300\301!\207 [heading 你好] 2]) (heading system))
    ```
2.  转义变量查询条件 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (setq test "你好")
       (setq query (org-ql--normalize-query '(and
                                              (heading "system")
                                              (ancestors (heading test))
                                              )))
       (message "语句：%s " query)
    ```
    
    ```text
    语句：(and (ancestors #[nil \301!\207 [test heading] 2]) (heading system))
    ```

通过上面两种语法的打印结果，第一种字面值的方式可以正确转换，第二种使用变量的方式时，该方法会将 `test` 的变量名，作为查询条件。 

第二种变量方式,做如下修改,实现正确解析变量值: 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq test "你好")
(setq query (org-ql--normalize-query `(and
                                       (heading "system")
                                       (ancestors (heading, test))
                                       )))
(message "语句：%s " query)
```

```text
语句：(and (ancestors #[nil \300\301!\207 [heading 你好] 2]) (heading system))
```

修改之后，打印结果和字面值一样，这表明已正常解析了变量，在查询后，能够得到想到内容。 

两种语法的主要差异在于: 

1.  语句1使用 `` ` `` 进行 quasiquote, 所以 (heading, test) 会作为一个 "heading 你好" 字面量进行查询。
2.  语句2使用 `'` 进行 quote, 所以 (heading test) 会作为一个字面量查询进行查询,等同于 (test heading)。


## 记录在单元测试用的案例 {#记录在单元测试用的案例}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; 使用互转方法 将普通语句转为字符串
(let ((query (org-ql--query-string-to-sexp (format "heading:%s" gts-claude-session)))
      (test)
      (buffers-files "prompt/prompt.org"))
  ;; 使用 select 语句验证，查询语句内部使用字面值和变量的问题
  (setq tess(org-ql-select buffers-files '(and (heading "system")(ancestors (heading "担任产品经理")))
                :action `(org-get-entry)))
  (message "测试222: %s" (car tess))
  ;; 使用 query 语句验证
  (setq test (org-ql-query
               :select '(org-get-entry)
               :from buffers-files
               :where `(and
                        (ancestors (heading, gts-claude-session))
                        (heading "system")
                        )
        ))

  (message "%s \n %s"gts-claude-session (car test))
  )
```

```text
担任产品经理
 请确认我的以下请求。请您作为产品经理回复我。我将会提供一个主题，您将帮助我编写一份包括以下章节标题的PRD文档：主题、简介、问题陈述、目标与目的、用户故事、技术要求、收益、KPI指标、开发风险以及结论。在我要求具体主题、功能或开发的PRD之前，请不要先写任何一份PRD文档。
```


## org-ql 转换问题：不支持 children parent  ancestors 相关属性的嵌套 {#org-ql-转换问题-不支持-children-parent-ancestors-相关属性的嵌套}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((result))
 (setq result (org-ql--query-sexp-to-string '(heading "quoted phrase" "word")))
(message "打印:%s" result)
;;(and
                        ;(heading gts-claude-session)
                        ;; (children (heading "system"))
                        ;)

 (setq result2 (org-ql--query-sexp-to-string '(ancestors
                                               (heading "universe"))))
(message "打印--:%s" result2)
)
```

```text
打印--:nil
```

