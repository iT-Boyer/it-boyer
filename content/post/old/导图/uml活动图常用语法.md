---
title: uml活动图常用语法
date: 2018-06-04 21:27:15
updated: 2018-09-22 21:18:58
comments: true
tags: [UML]
categories: 
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
```
start
:ClickServlet.handleRequest();
:new page;
if (Page.onSecurityCheck) then (true)
:Page.onInit();
if (isForward?) then (no)
:Process controls;
if (continue processing?) then (no)
stop
endif

if (isPost?) then (yes)
:Page.onPost();
else (no)
:Page.onGet();
endif
:Page.onRender();
endif
else (false)
endif

if (do redirect?) then (yes)
:redirect process;
else
if (do forward?) then (yes)
:Forward request;
else (no)
:Render page template;
endif
endif
stop
```
```puml
start
:ClickServlet.handleRequest();
:new page;
if (Page.onSecurityCheck) then (true)
:Page.onInit();
if (isForward?) then (no)
:Process controls;
if (continue processing?) then (no)
stop
endif

if (isPost?) then (yes)
:Page.onPost();
else (no)
:Page.onGet();
endif
:Page.onRender();
endif
else (false)
endif

if (do redirect?) then (yes)
:redirect process;
else
if (do forward?) then (yes)
:Forward request;
else (no)
:Render page template;
endif
endif
stop

```

title `uml模型图题目支持MD`
center header
`在此处添加标头`
endheader
start '开始'
'>>>>> 活动关系模块 支持嵌套，条件/循环/并行>>>>>>'
if(`环境条件`) then (`分流线名`)
:`分支1活动`;
-[`颜色`]-> `线备注`;
note left
`活动备注`
end note
elseif(`分流线名`)
:`分支2活动`;
else (`分流线名`)
:`分支3活动`;
endif

repeat
:`循环活动`;
repeat while (`环境条件`)

while (`环境条件`)
:`循环活动`;
endwhile

fork
:`并行活动`;
fork again
:`并行活动`;
end fork

'&&&&& 活动图组合模块 &&&&&&'
partition `活动组名`{
:`单元活动名称`;
}

|`#颜色` | `泳道名称`|
:`当前泳道活动`;


stop '结束／end关键字'
center footer
`在此处添加脚注`
endfooter
