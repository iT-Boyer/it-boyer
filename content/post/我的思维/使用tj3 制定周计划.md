+++
title = "使用tj3 制定周计划培养七习知识体系"
description = "使用项目管理的方式制定日常学习计划，有目的有产出，搭建知识体系"
date = 2021-11-23T14:59:00+08:00
publishDate = 2021-06-23T00:00:00+08:00
lastmod = 2021-11-23T14:59:27+08:00
categories = ["我的思维"]
draft = false
authors = ["iTBoyer"]
+++

## 目的 {#目的}

在学习tj3 过程中，考虑到是针对项目管理才会用到的技巧，学习中途搁置，没有产生归档文档。  

本次，受到亿学堂课程计划和七个习惯周计划的思想中启发到：  

长期困扰的无法坚持阅读的原因是什么？  

在培养七习的过程中，效率低，无法在生活中实践的原因是什么？  

在拆为己用学习中，吃的苦头，到头来，很少在阅读中实战，为什么带来的更多的是焦虑？  

在知识体系搭建的思想萌发，到现在举步维艰，是什么绑架了执行力？  

以上这些问题，都在折磨着，坠入了无法挣脱的困境。  

过程做了很多反思，总的来说，在理论、感慨的表面上做文章，很难从根本上剔除困扰。  

转换思维：  

在亿学堂12 堂课的引导下，重新认识了塑造富人思维的过程，它短期课程的安排，进度计划，可以在繁忙的工作中，点亮很多人学习的欲望，它的亮点在哪里？  

仔细分析对比，它是执行了以终为始，要事第一，激发了群众的积极主动的学习动力，它让人看到了成功的希望。  

如何嫁接这种前期的备课技巧，协助在七习中克服伤处几点困惑，这里已经推断出亿学堂的基本逻辑：  

设定目标-- 撰写宣言 -- 制定计划 -- 每日任务 -- 拆为己用 -- 输出感悟 -- 看见更好的自己  

在认识到自己的目的，如何在emacs 中真正的实践制定计划，激发行动力。第一想到的是甘特图，使用tj3 实现项目管理和跟踪的方式，看到每天的努力，看到目标的一步步实践的整个过程的渴望。  


## 项目管理提升效率 {#项目管理提升效率}

之前学tj3 的初衷是它是uml 甘特图的替代品，也是agenda 视图实现任务可视化管理工具。没有深刻认识到tj3项目管理的优点不止这些： [TaskJuggler - A Free and Open Source Project Management Software - About Task...](https://taskjuggler.org/)  

1.  TaskJuggler是一款现代、强大、开源的项目管理工具。和常用的甘特图编辑工具相比，新的 `项目规划` 和 `跟踪方法` 更灵活，更优越。
2.  它适用于从开始的一个想法到项目完成的整个过程。即在项目范围、资源分配、成本和收入规划；风险管理和通信管理方面有有很好的实践方式。
3.  tj3提供了一套优秀的项目计划功能，会根据项目大纲和你提供的约束，计算项目时间线和资源分配。
4.  内置资源平衡器和一致性检查器，如果项目出现问题，将会卸载你不必担心的详细信息。
5.  灵活的as-many-details-as-necessary方法允许你在开始时规划项目，使它的对于新的管理策略( 如极限编程和敏捷项目管理) 也非常理想。

[Exporting Gantt charts with Taskjuggler v3 (tj3)](https://orgmode.org/worg/exporters/taskjuggler/ox-taskjuggler.html)  

[使用Org-mode和TaskJuggler管理项目信息](https://leiyue.wordpress.com/2012/07/04/use-org-mode-and-taskjuggler-to-manage-to-project-information/)  

[使用Org-mode + taskjuggler进行项目管理 | Z.Y. ☯ Cosmos](https://izhen.me/2021/05/23/org-task-doc/)  

[Planning with Org-Mode and Taskjuggler - New York Stag Hunts](http://space.af/blog/2020/06/11/planning-with-org-mode-and-taskjuggler/)  


## 安装使用 {#安装使用}

1.  tj3 和 plantuml 对比之前学习过plantuml 建模，搜集了很多便于编写uml 的代码快，在emacs 中编写，预览，同时集成到hugo 文章中一气呵成。  
    
    plantuml 也提供了一套gantt 图的绘制支持，有一套语法需要标记任务开始，结束，下一项，里程碑的注脚语法。用起来虽然方便，但是和实际的任务进度跟踪不是很好，想实现任务计划便于实时的状态同步到甘特图上是比较费事的。  
    
    有了tj3的支持，它提供了从org 中导出图，方便更新进度。
2.  创建一个学习博客，记录学习过程，包括环境搭建，语法使用，导出操作
3.  导出到hugo目录中，实现在hugo中预览进度
4.  创建博客记录\`tj3\` 的使用方法。

安装 `gem install --local taskjuggler`  

安装终端之后，重新启动emacs, 执行导出操作时，会显示taskjuggler 选项。  

相关属性可以自定义的当前变量列表： org-taskjuggler-default-global-header org-taskjuggler-default-global-properties org-taskjuggler-default-project-duration org-taskjuggler-default-project-version org-taskjuggler-default-reports: 设置要使用的报告文件。这可以以文本格式写入（如设置为完整报告文本字符串的变量），或（可能更容易）.tji写入包含报告定义的文件。 org-taskjuggler-extension org-taskjuggler-final-hook org-taskjuggler-keep-project-as-task org-taskjuggler-process-command org-taskjuggler-project-tag org-taskjuggler-report-tag org-taskjuggler-reports-directory org-taskjuggler-resource-tag org-taskjuggler-target-version: 应该设置为命令的输出tj3 --version，例如(setq org-taskjuggler-target-version 3.4) org-taskjuggler-valid-report-attributes org-taskjuggler-valid-resource-attributes org-taskjuggler-valid-task-attributes  

接下来就是学习任务属性，当加入到task 属性，就可以很方便的支持task 导出。  


## 熟悉 Taskjuggler {#熟悉-taskjuggler}


### tj3 项目的组成的部分 {#tj3-项目的组成的部分}

1.  项目的基本信息：（开始日期、持续时间 ( +4m)、日期/时间语法、时区等）
2.  帐号：Accounts （如果您不跟踪财务，则不适用）
3.  可用资源：resource 包含工资、假期、工作时间/天信息等。
4.  里程碑：Top level milestones
5.  任务：Tasks, 按项目主要组成，分解为可执行的任务
6.  报告：可定义的报告


### 常用设置 {#常用设置}

1.  定义任务时间 start、end、depends、maxstart和maxend [start](http://www.taskjuggler.org/tj3/manual/start.html)
2.  定义任务持续时间 Effort、duration、length  
    1.  `duration`: 指定任务应该持续的时间。这是日历时间，不是工作时间。7d 表示一周。  
        
        如果指定了资源，则在可用时分配它们。资源的可用性对任务的持续时间没有影响。它将始终是指定的持续时间。  
        
        如果使用此属性，任务可能没有子任务。设置此属性将重置 `Effort` 和 `lenght` 属性。  
        
        duration <value> (min | h | d | w | m | y)
    
    2.  `length` : 将此任务的持续时间指定为工作时间，而不是日历时间。7d 表示 7 个工作日，或 7 次 8 小时（假设默认设置），而不是一周。  
        
        具有长度规范的任务可能具有资源分配。资源在可用时分配。不能保证任务将获得分配的任何资源。资源的可用性对任务的持续时间没有影响。  
        
        如果没有全球假期，那么没有指定资源可用的时间段仍被视为工作时间，并且相应地定义了全球工作时间。  
        
        对于长度计算，除非任务已分配班次，否则全局工作时间和全局休假很重要。在后一种情况下，轮班的工作时间和假期适用于指定的时间段，以确定某个时段是否为工作时间。如果资源定义了额外的工作时间，则长度为 5d 的任务很可能会分配超过 40 小时的工作量。资源工作时间仅影响是否为特定时间段进行分配。它们不会影响任务的最终持续时间。  
        
        如果使用此属性，任务可能没有子任务。设置此属性将重置 `duration~、~Effort` 和 `milestone` 属性。  
        
        length <value> (min | h | d | w | m | y)
    3.  `Effort` : 指定完成任务所需的工作量。 `Effort: 6d` （6资源天）可与2专职资源情况下，在3个工作日内完成。  
        
        需要至少一个定义并分配给任务的资源，并将使用他的/她可用的工作时间/天，以确定任务需要多长时间。  
        
        在分配的资源贡献了指定的工作量之前，任务不会完成。因此，任务的持续时间将取决于所分配资源的可用性。指定的 `Effort值` 必须至少与 `timingresolution` 一样大。  
        
        警告：在几乎所有现实世界的项目中， `Effort` 并不是时间和资源的产物。仅当可以在不增加任何开销的情况下对任务进行分区时，这才是正确的。有关这方面的更多信息，请阅读Frederick P. Brooks, Jr. 的The Mythical Man-Month。  
        
        如果使用此属性，任务可能没有子任务。设置此属性将重置 `duration` 持续时间和 `length` 长度属性。具有 `Effort值` 的任务不能成为里程碑。  
        
        effort <value> (min | h | d | w | m | y)

3.  定义依赖有多种方法可以定义任务之间的依赖关系。当来自 Org 模式背景时，您可能希望使用 Org 模式提供的工具来定义它们，它们是  
    1.  `ORDERED` ： 该ORDERED属性允许您声明子任务必须按照它们出现的顺序完成（最上面在前）。
    2.  `BLOCKER` ： 允许您声明任务依赖于以下任一者的属性  
        1.  值为 `previous-sibling` 时，依赖上一个item 的task 对象
        2.  值为某个任务的task\_id  时，依赖对应的task
    3.  `depends` ：定义在指定的任务完成之前不能启动任务，通过指定task\_id 确定依赖关系。

4.  其他汇总  
    
    tj3 定义一个任务的几个属性，任务名称，任务Id,任务依赖，任务评估时长，开始，结束时间等：

<!--listend-->

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
* TODO %^{Project name} [0%] :taskjuggler_project:
DEADLINE: %^t
:PROPERTIES:
:CREATED: %U
:task_id:
:ORDERED: 任务关联
:Effort:   ; 评估工作量 表示需要的工作时，而不是日历时
:BLOCKER:  ; 前置工作
:efficiency: ;表示资源的效率，默认1.0。设置举例：如果一个人顶三个，则设置为3.0即可
:priority: ;任务优先级，0~1000之间的整数，如果资源数小于当时需要被配置的任务数时，则根据优先级自己安排
:limits:   ; 最大工作时间
:leaves:   ; leaves 请假
:allocate: ; 角色分配
:resource_id: ; 资源id
:depends: 前置任务
:rate: 费率
:duration: 持续时间
:END:
%^{OUTCOME}p
%?

** Milestones
{{< /highlight >}}


### Taskjuggler 语法到 Org-mode 的映射 {#taskjuggler-语法到-org-mode-的映射}

如果您熟悉 Taskjuggler，那么了解 Taskjuggler 语法如何映射到 Org-mode 可能会有所帮助。这是通过属性[Property Syntax (The Org Manual)](https://orgmode.org/manual/Property-syntax.html#Property-syntax) 完成的，这是属性适用的标题下方的一个简单的键值对。  

大部分实际的 Taskjuggler 项目由task组成，这些task可以被赋予各种属性。可以在 tj3 手册的[task 语法](http://www.taskjuggler.org/tj3/manual/task.html) 规范中查看允许属性的完整列表。  

1.  Taskjuggler 中的任务可能如下所示：

<!--listend-->

{{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
task entry_door "Install entry door" {
  depends buy_door
  effort 4h
}
{{< /highlight >}}

在此示例中， `task` 将以下文本定义为 Taskjuggler 编译的任务。 `entry_door` 是任务的 ID，它在当前子树中必须是唯一的。引号内 `"Install entry door"` 作为报告中的任务名称。  

此任务有两个属性： `depends` 和 `effort` 。在此示例中， `entry_door` 依赖于另一个任务 ， `buy_door` 估计所需的工作量是 4 小时。  

在 Org 模式下，等效的任务会像这样创建：  

{{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
* Install entry door
:PROPERTIES:
:task_id:  entry_door
:depends:  buy_door
:Effort:   4h
:END:
{{< /highlight >}}

一些属性可以通过两种方法设置。例如，任务的开始可以由 `SCHEDULED` 标签或 `:start:` 属性定义；以下是等效的：  

{{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
* Install entry door
   SCHEDULED: <2013-07-15 Mon>

* Install entry door
  :PROPERTIES:
  :start:    2013-07-15
  :END:
{{< /highlight >}}

同样 `DEADLINE` 时间戳和 `:end:` 属性函数的方式相同。  

设计一个场景来计划下任务：  

比如这周，要完成及件事情，练习使用tj3 方式跟踪任务。同时考虑是否需要建立模板来快速创建tj3 任务。先看下任务和tj3 之间的差异。  


## 问题 {#问题}

1.  导出时，需要设置为buffer 模式下导出，才能够显示全部任务状态。
2.  属性不确定很大，不适合创建snippets 模板，不建议使用模板或代码块。
3.  要想计划一周的任务还需要进一步熟悉tj3 的用法，熟练分析任务进度，提高习惯培养的积极性
4.  支持指定忽略tag: `:EXPORT_EXCLUDE_TAGS: ignore noexport`


### 实战问题 {#实战问题}

1.  有子任务时，不要在该节点添加effort 属性
2.  在tj3 一级节点上，不要添加ordered 代替方案：使用节点中的blocked 属性控制依赖，显示甘特图上的顺序
3.  在二级节点上，直接开启ordered ，有助于子任务的排序显示
4.  当声明了tj3\_resource 资源之后，导出会提示失败。需要在task 任务中关联资源之后，再次导出就正常了。
5.  甘特图和资源图 -- 精确到周、日、小时的设置方式 taskreport 字段中 设置 chart


### 实战次序 {#实战次序}

1.  先定里程碑
2.  分解任务
3.  处理依赖关系
4.  确定里程碑
5.  在评估工作量
6.  声明资源
7.  分配资源


## 实战 {#实战}


### 项目的基础信息 {#项目的基础信息}

1.  设置项目周期：project-duration
2.  设置项目标识： project-tag
3.  重写项目基础信息的函数： `org-taskjuggler--build-project`  
    1.  添加 actual 基准支持
    2.  扩展字段 `电话`
    3.  重写task 任务信息函数 `org-taskjuggler--build-task`  
        1.  增加解析actual 字段代码  
            
            {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                           (actual (org-element-property :ACTUAL task))
            {{< /highlight >}}
        2.  在tjp 中增加actrual 字段  
            
            {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                          (and actual (format " actual:%s\n" actual))
            {{< /highlight >}}
            
            效果如下：  
            
            {{< highlight org "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                           ** 编写金和项目
                           :PROPERTIES:
                           :task_id:  jinher
                           :allocate: dev
                           :Effort:   1d
                           :actual: effort 1d start 2021-09-17
                           :END:
            {{< /highlight >}}
            
            导出tj3 之后的格式：  
            
            {{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=6-6 0-0" >}}
                             task jinher "编写金和项目" {
                               depends !swiftUI
                               purge allocate
                               allocate dev
                               effort 1d
                              actual:effort 1d start 2021-09-17
                             }
            {{< /highlight >}}
            
            {{< figure src="/ox-hugo/2021-09-07_18-08-13_截屏2021-09-07 下午6.08.04.png" width="80%" >}}
4.  新增列 `费用` 、 `收入` 在 `org-taskjuggler-default-global-properties` 中添加  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       account cost \"费用\"
       account revenue \"收入\"
       balance cost revenue
    {{< /highlight >}}
5.  导入报告模板  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (setq org-taskjuggler-default-reports '("include \"reports.tji\""))
    {{< /highlight >}}
6.  定制报告信息在 `reports.tji` 中查看语法
7.  在org 中无法制定输出目录，支持tj3 命令行。可以在发布时，使用tj3 命令，借助 `ourtputdir` 辅助更新相应的项目。 `:outputdir: "/Users/boyer/reports/"` 仅在命令行时有效。  
    
    替代方案：通过el 命令，指定目录  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (make-directory "~/reports/七个习惯")
       (setq org-taskjuggler-reports-directory "~/reports/七个习惯")
    {{< /highlight >}}
8.  task\_id 不能以数字开头
9.  `complate` 跟踪task状态：仅支持DONE/TODO 状态，它仅用于回报信息，不会更新分配、剩余任务等信息。 [Day\_To\_Day\_Juggling](https://taskjuggler.org/tj3/manual/Day%5FTo%5FDay%5FJuggling.html#Tracking%5Fthe%5FProject)
10. 设置里程碑和第一个task任务的依赖关系。设里程碑过程，需要注意第一个flag 应该被设置为起始任务，task\_id 设置为start ，第一个模块依赖这个flag, 后续flag 模块一次依赖为：blocker:previous-sibling 即可。
11. 使用journalentry设置日志 :journalentry: 2021-09-09 "设定任务标题头部" {alert red summary "事件"}
12. 使用tracereport新增scrum燃尽图 [Scrum Example Project - Product Burndown](https://taskjuggler.org/tj3/examples/Scrum/Product%20Burndown.html)  
    
    [tracereport](https://taskjuggler.org/tj3/manual/tracereport.html)  
    
    1.  设置project 的 `task_id` 为 `project`
    2.  在 `report.tji` 添加 `tracereport` burndown 模块。  
        
        {{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                   tracereport burndown "burndown" {
                       headline "燃尽图"
                       sorttasks id.up
                       width 1000
                       columns opentasks
                       # columns end { title "<-name->" }
                       hidetask plan.id != "project"
                   }
        {{< /highlight >}}
    3.  设置导出命令，支持跟踪功能  
        
        {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
        
               (setq org-taskjuggler-process-command "tj3 --add-trace --no-color --output-dir %o %f")
        {{< /highlight >}}


### 实例赏析 {#实例赏析}

[新产品上市计划 - 项目综述](https://it-boyer.github.io/iDocs/taskjuggler/demo-all/%E9%A1%B9%E7%9B%AE%E7%BB%BC%E8%BF%B0.html) [七个习惯学习计划 - 项目综述](https://it-boyer.github.io/iDocs/taskjuggler/%E4%B8%83%E4%B8%AA%E4%B9%A0%E6%83%AF/%E9%A1%B9%E7%9B%AE%E7%BB%BC%E8%BF%B0.html)
