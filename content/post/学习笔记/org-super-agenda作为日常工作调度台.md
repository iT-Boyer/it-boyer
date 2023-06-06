+++
title = "定制 org-super-agenda 视图作为任务调度台"
description = "博客简介"
date = 2023-05-27T20:35:00+08:00
lastmod = 2023-05-27T20:38:37+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

借助 org-super-agenda 加深理解 org-agenda 的功能和用法，通过分组模板养成 review 任务习惯,激发执行力! 

-   [X] 学习 org-super-agenda 加强管理，修炼二项限时间管理的工具 
    
    可以结合 org-ql 增强语法的可读性，结合 org-super-agenda 增加定义选择器的效果. 
    
    要把习惯三要事第一融入到 aganda 中。有几个特点要呈现出来:优先级 ：时间矩阵
-   [X] 使用 snippet 的方式执行 agenda 的导出操作，能够快速插入到当前 buffer ，实现在 capture 模板执行脚本的问题。
-   [X] 解决 capture 中获取 tag 的问题在 capture 模板中使用 ％^g 启动 tag 选择功能，然后在 gptel 就可以拿到 aitag,实通过 tag 动态设置 prompt 的功能。


## 理解 org-super-agenda 概念 {#理解-org-super-agenda-概念}

org-super-agenda 是基于 org-ql 针对 agenda 分组功能的增强工具，兼容内置的 org-agenda ，很大简化了配置 org agenda 的语法。主要用于 agenda 视图展示。 

在定义模板之前，先区分 org-ql ， org-super-agenda 和 org-agenda 的概念，至关重要。 

在学习 org-ql 插件之后，对 org-mode 有和更深的理解，它是强大的 org 解析工具，通过 org-ql 可以像查询数据库一样查询 org 文件，能够根据 todo 状态，优先级，标签等，快速获取条目内容，例如：条目的 heading ，entry。 

`org-ql 有丰富的工具，都支持 query 语法` 

1.  org-ql-query/org-ql-select 可以通过 query 语法，获取 org 元数据列表，在需要获取 org 信息作为参数，为其他方法提供数据。 
    
    例如： 为 capture 模板自动设置 role 角色。在 claude 翻译工具中，设定角色。在 snippet 中添加快捷键，快速获取 org 元数据，为自定义工具提供数据源的支持。
2.  org-ql-views 是 org-ql 内置 org-agenda 的分组视图，提供了今日，本周，下周，阻塞任务等视图入口。
3.  org-ql-block 可以配合 org-agenda 自定义命令，添加视图分组，简化 agenda 配置语法。
4.  helm-org-ql 基于 org-ql 的 helm 查询视图，支持 org-ql 语法搜索。

主要的学习方式，翻译文档，从文档中找灵感思路。后续可以在实践中，不断优化选择器的语法，现在不要纠结太多的用法，够用够入门就行。 


## 基于 org-super-agenda 定义 agenda 模板 {#基于-org-super-agenda-定义-agenda-模板}

想要通过 org-super-agenda 优化的工作流 

1.  7 天周期的方式，主要任务和目标。排序优先级，分类，标签等功能的使用。
2.  怎么结合 tj3 项目理念整合，出以 tj3 为模块的分组
3.  怎么结合阅读状态，看到现在的阅读量和安排的分组
4.  怎么看到习惯的进行状态，是否已经脱离的正常生活
5.  怎么把训练 prompt 作为项目，进行管理，呈现在 agenda 中，现在进行到什么进度等状态跟踪。
6.  怎么把七个习惯的状态找回来，能够执行自我意识的思考，呈现出自我领导的意识，第一次创造的快感。
7.  使用 auto-gpt 的在制定角色的时候，有使命，有目标，有原则，有计划的。第一次创造，第二次创造的流程的演示等。

要做的事情： 

-   [X] 新建 `+super-agenda.el` 专属的配置文件
-   [X] 演示 readme 初步配置，在 agenda 中看到效果
-   [ ] 结合七个习惯，梳理自己的愿景，计划，整理在 org 中管理

基本语法展示 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((org-super-agenda-groups
         '(;;每个组的选择器之间都有一个隐式布尔或运算符。
           (:name "Today"               ; name 可选，指定section名
            :time-grid t                ; 以时间表格样式显示项目
            :todo "TODAY")              ; 具有此todo关键字的项
           (:name "Important"
            ;; Single arguments given alone
            :tag "bills"
            :priority "A")
           ;;一次设置多个分组的顺序
           (:order-multi (2 (:name "Shopping in town"
                             ;; 同时符合两个 tag 条件的分组。
                             :and (:tag "shopping" :tag "@town"))
                            (:name "Food-related"
                             ;; 在列表中给出多个参数，隐式为0
                             :tag ("food" "dinner"))
                            (:name "Personal"
                             :habit t
                             :tag "personal")
                            (:name "Space-related (non-moon-or-planet-related)"
                             ;; regexp 在整个条目上不区分大小写地匹配
                             :and (:regexp ("space" "NASA")
                                   ;; Boolean NOT also has implicit OR between selectors
                                   :not (:regexp "moon" :tag "planet")))))
           ;;当没有给出节名时，组提供自己的节名 Groups supply their own section names when none are given
           (:todo "WAITING" :order 8)   ; Set order of this section
           (:todo ("SOMEDAY" "TO-READ" "CHECK" "TO-WATCH" "WATCHING")
            ;; Show this group at the end of the agenda (since it has the
            ;; highest number). If you specified this group last, items
            ;; with these todo keywords that e.g. have priority A would be
            ;; displayed in that group instead, because items are grouped
            ;; out in the order the groups are listed.
            :order 9)
           (:priority<= "B"
            ;; Show this section after "Today" and "Important", because
            ;; their order is unspecified, defaulting to 0. Sections
            ;; are displayed lowest-number-first.
            :order 1)
           ;; After the last group, the agenda will display items that didn't
           ;; match any of these groups, with the default order position of 99
           )))
    (org-agenda nil "a"))
```


### <span class="org-todo todo TODO">TODO</span> 每日安排 {#每日安排}

在日常工作安排中，出现最多的视图，通过这个配置，养成跟踪任务状态习惯。 

它能一目了然的将正在进行的，即将开始的，快到期，已到期，阻塞等任务状体啊的统计视图，能够帮助检查时间安排，排查执行中遇到存在的问题等。 

要想摆脱时间黑洞，就要充分利用这个配置，时刻知道自己的状态和重要的事情是什么，要做的事情等。 

使用 `org-super-agenda-daily` 快速 review 日常任务状态： 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq org-super-agenda-daily
        '((:name "日程安排\n"
           :time-grid t)
          (:name "今日要事\n"
           :scheduled today)
          (:name "今日期限\n"
           :deadline today)
          (:name "超期事务\n"
           :deadline past)
          (:name "即将截止\n"
           :deadline future)
          (:name "待处理\n"
           :todo "WAIT"
           :order 98)
          (:name "Scheduled earlier\n"
           :scheduled past)))
(let ((org-super-agenda-group org-super-agenda-daily))
  (org-agenda-list))
```


### <span class="org-todo todo TODO">TODO</span> 每周安排 {#每周安排}

每周安排，以七个习惯的中的设定来配置，回顾的思路来配置这项视图。 

先定角色，在看角色下的任务，再看重要的事情，紧迫的事情，使用二象限思维，分析观察工作的进度和时间管理。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq org-super-agenda-habit '(
                               (:name "我的" :tag "@我的"
                                :scheduled (get-last-day-of-week-string))
                               (:name "阅读" :tag "阅读者")
                               (:name "理财" :tag "投资者")
                               (:name "开发" :tag "开发者")
                               (:name "管理者" :tag "管理者")
                               (:name "兄弟" :tag "兄弟")
                               ))
(let ((org-super-agenda-group org-super-agenda-habit))
  (org-agenda-list))
```


### <span class="org-todo todo TODO">TODO</span> 快速导出 agenda 清单模板 {#快速导出-agenda-清单模板}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq org-super-agenda-snippet '(
                                 (:name "我的" :tag "@我的"
                                  :scheduled (get-last-day-of-week-string))
                                 ))
(let ((org-super-agenda-group org-super-agenda-snippet))
  (org-agenda-list))
```


### tj3 在 agenda 的分组 {#tj3-在-agenda-的分组}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(setq org-super-agenda-tj3 '(
                             (:name "我的" :tag "@我的"
                              :scheduled (get-last-day-of-week-string))
                             ))
(let ((org-super-agenda-group org-super-agenda-tj3))
  (org-agenda-list))
```


### 日志模式 {#日志模式}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((org-super-agenda-groups
       '((:order-multi (1 (:name "Done today"
                                 :and (:regexp "State \"DONE\""
                                               :log t))
                          (:name "Clocked today"
                                 :log t))))))
  (org-agenda-list))
```


## 辅助测试的方法和模拟数据 {#辅助测试的方法和模拟数据}


### 获取本周的最后一天的日期字符串 {#获取本周的最后一天的日期字符串}

以下是一个可以获取本周最后一天日期字符串的Elisp函数： 

```elisp
(defun get-last-day-of-week-string ()
  (let* ((curr-time (current-time))
         (day-of-week (string-to-number (format-time-string "%w" curr-time)))
         (days-to-sunday (- 7 day-of-week))
         (last-day-of-week (time-add curr-time (days-to-time days-to-sunday))))
    (format-time-string "%Y-%m-%d" last-day-of-week)))
(message "This week's last day is: %s" (get-last-day-of-week-string))
```

该函数使用了Emacs Lisp的时间函数，计算出本周最后一天的日期，再用 `format-time-string` 函数将其转换成指定格式的字符串。最后返回日期字符串。 


### 测试数据 {#测试数据}


#### testA-RRRRRRRRRRRREEEEEEEEEEEE <span class="tag"><span class="GPT">GPT</span></span> {#testa-rrrrrrrrrrrreeeeeeeeeeee}


#### testB-RRRRRRRRRRRREEEEEEEEEEEE <span class="tag"><span class="TET">TET</span></span> {#testb-rrrrrrrrrrrreeeeeeeeeeee}


#### testC-RRRRRRRRRRRREEEEEEEEEEEE <span class="tag"><span class="KEY">KEY</span></span> {#testc-rrrrrrrrrrrreeeeeeeeeeee}


#### testD-RRRRRRRRRRRREEEEEEEEEEEE <span class="tag"><span class="IT">IT</span></span> {#testd-rrrrrrrrrrrreeeeeeeeeeee}


#### TODAY testE-RRRRRRRRRRRREEEEEEEEEEEE <span class="tag"><span class="IT">IT</span></span> {#today-teste-rrrrrrrrrrrreeeeeeeeeeee}

<span class="timestamp-wrapper"><span class="timestamp">&lt;2023-05-21 周日&gt;</span></span> 

