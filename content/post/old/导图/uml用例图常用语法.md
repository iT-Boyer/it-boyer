---
title: uml用例图常用语法
date: 2018-06-04 21:20:41
updated: 2018-09-22 21:18:59
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
:Main Admin: as Admin
(Use the application) as (Use)

User -> (Start)
User --> (Use)

Admin ---> (Use)

note right of Admin : This is an example.

note right of (Use)
A note can also be on several lines
end note

note "This note is connected\nto several objects." as N2
(Start) .. N2
N2 .. (Use)
```
效果图
```puml
:Main Admin: as Admin
(Use the application) as (Use)

User -> (Start)
User --> (Use)

Admin ---> (Use)

note right of Admin : This is an example.

note right of (Use)
A note can also be on several lines
end note

note "This note is connected\nto several objects." as N2
(Start) .. N2
N2 .. (Use)
```

title `uml模型图题目支持MD`
center header
`在此处添加标头`
endheader

'*******  声明用例模块 usecase *******'
(`用例名称`) as (`别名`) <<`构造类型`>>

usecase `用例名称` as " `描述一`
--
`描述2`
==
`描述3`
.. `描述标题` ..
`描述4`
"
'*******  声明角色模块 actor *******'
:`角色名称`: as `别名`
actor `角色名称`



'---- 声明备注:用例线备注可以当做用例来参与到关系连接中---'
note "`备注内容`" as `备注对象`


'#####  备注模块 位置：left/right/top/bottom  #####'
note `位置`  of `用例/角色`: `描述信息`


'>>>>>>>>>>  关系模块  >>>>>>>>>>'
left to right dirction  '指定布局方向'
`角色` --> `用例`:`关系线描述`

'---- 用例关系中的备注对象 ----'
`角色` -->`备注对象`
`备注对象` -->`用例`

center footer
`在此处添加脚注`
endfooter


