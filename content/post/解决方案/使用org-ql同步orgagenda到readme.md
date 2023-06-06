+++
title = "动态生成 README.md 四象限数据： org-ql 查询语法和分组规则"
description = "博客简介"
date = 2023-05-31T20:45:00+08:00
lastmod = 2023-06-01T04:35:21+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

制定要显示的是个区域的 org-ql 查询条件，和 org-supper-agenda 分组规则 

在使用 org-ql 导出数据之前，先确定四象限中，需要什么数据，例如，今日要务，一周安排等。再根据 org-ql 语法查询 org 文件中的日程安排，然后，使用 org-supper-agenda 分组的规则，导出自定义视图格式的任务清单，再将组的内容，更新替换到 README.md 文件中，最终达到同步的效果。 

按照自己的习惯，定义四象限区间将要显示的内容，然后，根据内容需求，编写 org-ql 查询和分组导出。 

1.  今日毕：显示今天到期，开始，待处理的任务，显示项目内容的任务。 
    1.  需要先确定查询语句，不能是项目，而是可行性的任务，不能有子条目。必须有父条目，
    2.  分组的规则，按时间，优先级排序即可。
2.  周安排：显示本周的主要项目和任务目标。里程碑周任务的特殊性，需要是项目，目标，里程碑。 
    1.  查询条件：需要按照 tag 来区分项目，列出每个项目的前三个任务。要限制在本周完成的任务。
    2.  分组规则：按优先级，条目等级分组即可
3.  猴子法则：灵感和待处理的任务 
    1.  收集箱灵感收集的条目，待处理状态。搜索条件，inbox 标签，todo 关键字。
    2.  分组规则：按优先级和时间消耗排序。
4.  娱乐，健身，阅读，跑步。娱乐性的活动，主要为了健康和精神调节，充电的事项 
    1.  有阅读，运动，跑步，习惯。查询条件，属性，habit
    2.  按习惯分组。按时间排序


## 编写查询今日任务和分组规则 {#编写查询今日任务和分组规则}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-ql-search (current-buffer)
  '(and
    (ts-active :on today)
    (todo "TODO")
    (ancestors (tags "project"))
    (or (not (children))
        (children (not (todo "TODO"))))
    )
  :sort '(todo priority date)
  :super-groups '((:name "今日毕"
                   :scheduled today
                   :deadline today
                   :priority "A"))
  )
```

```text
1
```


## 周任务查询条件和分组规则 {#周任务查询条件和分组规则}

周安排：显示本周的主要项目和任务目标。里程碑周任务的特殊性，需要是项目，目标，里程碑。 

1.  查询条件：需要按照 tag 来区分项目，列出每个项目的前三个任务。要限制在本周完成的任务。
2.  分组规则：按优先级，条目等级分组即可

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-ql-search (current-buffer)
  '(and
    (parent (and
                (tags "project")
                (not (parent))
               ))
    (children (todo "TODO"))
    ;(ancestors
    ;       (and (tags "project")
    ;       (children)))
    )
  :sort '(todo priority date)
  :super-groups '((:auto-parent t))
  )
```

```text
1
```


### <span class="org-todo todo TODO">TODO</span> 如何在模板中动态指定本周期限 {#如何在模板中动态指定本周期限}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(cons "README weekly" (lambda ()
                        (interactive)
                        (let* ((plist)
                               (ts (ts-now))
                               (beg-of-week (->> ts
                                                 (ts-adjust 'day (- (ts-dow (ts-now))))
                                                 (ts-apply :hour 0 :minute 0 :second 0)))
                               (end-of-week (->> ts
                                                 (ts-adjust 'day (- 6 (ts-dow (ts-now))))
                                                 (ts-apply :hour 23 :minute 59 :second 59)))
                               (plist (list :buffers-files (org-agenda-files)
                                            :query `(and
                                                     (ts-active :from ,beg-of-week
                                                                :to ,end-of-week)
                                                     (parent '(and
                                                               (tags "project")
                                                               (not (parent))))
                                                     (children (todo "TODO"))
                                                     )
                                            :title "周计划"
                                            :sort '(todo priority date)
                                            :super-groups '((:auto-parent t)))))
                          plist)))
```


## 三象限搜索条件和分组规则 {#三象限搜索条件和分组规则}

猴子法则：灵感和待处理的任务 

1.  收集箱灵感收集的条目，待处理状态。搜索条件，inbox 标签，todo 关键字。
2.  分组规则：按优先级和时间消耗排序。

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-ql-search (current-buffer)
  '(and
    (todo "TODO")
    (tags "inbox")
    (not (children))
    )
  :sort '(todo priority date)
  :super-groups '((:name "Inbox"
                   :tag "inbox"))
  )
```

```text
1
```


## 四象限搜索条件和分组规则 {#四象限搜索条件和分组规则}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-ql-search (org-agenda-files)
  '(and
    (not (done))
    (not (tags "project"))
    (or (deadline auto)
        ;; (habit)
        (scheduled :to today)
        (ts-active :on today)
        )
    )
  :sort '(todo priority date)
  :super-groups '((:name "充电模式"
                   :habit t)
                  (:name "阅读"
                   :tag "reader")
                  (:name "运动"
                   :tag "sport")
                  (:name "写作"
                   :tag "writer"))
  )
```

```text
1
```

专注模式，阅读，运动，写作 

```text
1
```


## 在 emacs 中显示总览效果 {#在-emacs-中显示总览效果}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(org-ql-search (org-agenda-files)
  '(or (todo) (habit) (tags "inbox"))
  :sort '(todo priority date)
  :super-groups '((:name "dairy"
                   :scheduled today
                   :and (:tag "project" :children nil)
                   )
                  (:name "week"
                   :and (:tag "project" :children t)
                   )
                  (:name "index"
                   :tag "inbox")

                  (:name "habit"
                   :habit t
                   :tag "writer")))
```

```text
1
```


## 模拟数据：如何培养好习惯 <span class="tag"><span class="project">project</span></span> {#模拟数据-如何培养好习惯}


### <span class="org-todo todo TODO">TODO</span> 尝试新的思维方式 <span class="tag"><span class="inbox">inbox</span></span> {#尝试新的思维方式}


### <span class="org-todo todo TODO">TODO</span> 阅读七个习惯的书籍 {#阅读七个习惯的书籍}


#### 整理书记 {#整理书记}


### <span class="org-todo todo TODO">TODO</span> 撰写自己的使命宣言 {#撰写自己的使命宣言}


#### <span class="org-todo todo TODO">TODO</span> 熟悉撰写步骤 {#熟悉撰写步骤}


#### <span class="org-todo todo TODO">TODO</span> 使用30 分钟完成撰写 {#使用30-分钟完成撰写}


### <span class="org-todo todo TODO">TODO</span> 和人交流分享经验 {#和人交流分享经验}


#### <span class="org-todo todo TODO">TODO</span> 线上论坛讨论 {#线上论坛讨论}


#### <span class="org-todo todo TODO">TODO</span> 线下聚会交流 {#线下聚会交流}


#### <span class="org-todo todo TODO">TODO</span> 周日跑步运动 {#周日跑步运动}


### <span class="org-todo todo TODO">TODO</span> 下周举行聚餐活动 {#下周举行聚餐活动}


## 一周阅读一本小说 {#一周阅读一本小说}


### <span class="org-todo todo TODO">TODO</span> 十万个为什么 <span class="tag"><span class="reader">reader</span></span> {#十万个为什么}


### <span class="org-todo todo TODO">TODO</span> 钢铁是怎么炼成的 <span class="tag"><span class="writer">writer</span></span> {#钢铁是怎么炼成的}

