+++
title = "taskjuggler导出xml和csv方法实战"
description = "熟悉tj3命令，从tjp中导出更好的格式报告，有利于在其他平台上数据共享。"
date = 2021-10-20T15:27:00+08:00
publishDate = 2021-10-20T14:08:00+08:00
lastmod = 2021-10-20T15:33:02+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## A1:追问和反思经验 {#a1-追问和反思经验}

在配合org导出tj3生成html回报格式，有了很好的成效，现在工作管理要迁移到禅道平台，联想到到tjp格式的文件导出通用的格式，就可以很方便的同步到禅道平台上。  

现在就着手做一些导出工作的延伸，将org格式导出tjp然后在生成通用的MS-project，CSV格式来共享计划。  


## I: 分析和整理信息知识点 {#i-分析和整理信息知识点}

在org中做到了快速一键导出 `spc m e j o` ,生成甘特图html版的任务管理看板。  

可以参考之前配置记录：[七个习惯和taskjuggler整合]({{< relref "整合七个习惯和taskjuggler工具" >}})  

要导出xml，csv格式，应该在配置上大同小异，先确定如何导出，是否需要额外的tj3命令的学习。  


## A2:内化应用知识，谱写愿景 {#a2-内化应用知识-谱写愿景}

在确定导出xml 和csv 找到了部分案例：  


### export 导出通用文件格式 {#export-导出通用文件格式}

[export 语法：The TaskJuggler User Manual](https://taskjuggler.org/tj3/manual/index.html)  


#### `export` 的~formats~仅支持两种文件格式导出 {#export-的-formats-仅支持两种文件格式导出}

1.  MS-xml：支持在win 系统上通过 Project 应用打开。在Mac 上使用OmniPlan 工具打开。
2.  tjp/tji ： 暂时没有用到。

从案例中可以知道，可以通过 `export` 语法来实现导出设置：  

{{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
# Export the scheduled project as Microsoft Project XML format.
export "MS-Project" {
  formats mspxml
  loadunit quarters
}
{{< /highlight >}}


#### export 节选任务导出 {#export-节选任务导出}

可以制定导出的哪类任务和指定要导出那个时间段的任务。  

也可以指定 `formats属性` 导出 xml/tjp 的格式。  

{{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
# Export only bookings for 1st week as resource supplements
export "Week1Bookings" {
  definitions -
  start 2021-09-28
  end 2021-10-15
  taskattributes Bookings #*
  hideresource 0
}
{{< /highlight >}}

<!--list-separator-->

-  使用节选功能

    因为这个功能，需要手动指定时间和分类等属性设置。  
    
    暂时仅知识手动导入，不支持org 导出。  
    
    步骤如下：  
    
    1.  先导出项目，即执行 spc m e j o
    2.  在~include~ 文件添加导出设置，指定时间等属性  
        
        1.  formats: 文件格式
        2.  start: 截取开始时间点
        3.  end: 截取的时间点
        
        <!--listend-->
        
        {{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
           # Export only bookings for 1st week as resource supplements
           export "Week1Bookings" {
               formats msxml
             definitions -
             start 2021-09-28
             end 2021-10-15
             taskattributes Bookings #*
             hideresource 0
           }
        {{< /highlight >}}
    3.  使用命令重新导出项目，会当前目录生成一个新文件：例如 `Week1Bookings.xml` 。  
        
        {{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
           tj3 --add-trace ~/hsg/iNotes/content-org/project.tjp
        {{< /highlight >}}


### csv 格式任务导出 {#csv-格式任务导出}

1.  `textreport` ：支持csv,html,niku  
    
    但是遇到问题： `textreport 'TODO_CSV' cannot be converted into CSV format`

2.  `export` ： 支持 msxml/tjp 格式。

由于 `textreport` / `export` 都不支持cvs 格式。  

1.  `taskreport` 导出cvs  
    
    `taskreport`: 支持 csv, html, niku

只需要指定 taskreport 的formats 属性指定为csv 即可，同样支持其他属性。  

{{< highlight taskjuggler "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
taskreport TODO_CSV "简报" {
    formats csv
    # columns name
    columns bsi {title 'WBS'},
            name {title '名称'},
            start{title '开始日期'},
            end{title '结束日期'},
            status{title '状态'},
            complete{title '进度'},
            resources{title '资源'},
            note {title '备注' width 150}
    hideresource @all
    loadunit quarters
    scenarios actual
    caption '工作量以每人每天计算。'
}
{{< /highlight >}}


## 要事第一，制定下一步行动 {#要事第一-制定下一步行动}

现在已经有了两种格式的文件，通过整理，实现了org 导出多种格式的流程，统一导出到了归档项目工程目录下：~iDocs/taskjuggler/项目名/.csv/.xml~  

因为在项目下，文件统一的名称： `简报.csv`, `project.xml`  

分享路径： `https://it-boyer.github.io/iDocs/taskjuggler/连锁集团/简报.csv`  

分享路径： `https://it-boyer.github.io/iDocs/taskjuggler/连锁集团/project.xml`  

放在了githubpage更方便下载分享。  

在实现导出功能之后，具体使用场景有哪些：  


### MS-xml 的优势 {#ms-xml-的优势}

1.  导出MS-xml 可以通过OmniPlan 检查项目的合理性，在OmniPlan 专业的角度优化项目管理细节。
2.  在OmniPlan 可以导出更美观的格式效果，生成pdf 更显得专业易懂。
3.  可以节选一周任务/一月任务等，分段导出，只需要关注本周月的任务，能很大的简化了任务管理复杂度。


### csv 的优势 {#csv-的优势}

csv 格式简单，在共享数据时有很大的优势。  

{{< highlight csv "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
"WBS";"名称";"开始日期";"结束日期";"状态";"进度";"资源";"备注"
1;"连锁集团";"2021-09-28 09:00";"2021-10-12 17:00";"done";"100%";"";""
1.1;"  立项";"2021-09-28 09:00";"2021-10-12 17:00";"done";"100%";"";""
{{< /highlight >}}

使用macs 快速预览的效果，相当清新：  

{{< figure src="/ox-hugo/2021-10-20_14-58-55_截屏2021-10-20 下午2.58.51.png" width="80%" >}}
