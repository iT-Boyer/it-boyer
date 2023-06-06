---
title: uml类图常用语法
date: 2018-06-04 20:49:36
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
class BaseClass
namespace net.dummy #DDDDDD {
.BaseClass <|-- Person
Meeting o-- Person
.BaseClass <|- Meeting
}
namespace net.foo {
net.dummy.Person <|- Person
.BaseClass <|-- Person
net.dummy.Meeting o-- Person
}
BaseClass <|-- net.unused.Person
```
效果图：
```puml
class BaseClass
namespace net.dummy #DDDDDD {
.BaseClass <|-- Person
Meeting o-- Person
.BaseClass <|- Meeting
}
namespace net.foo {
net.dummy.Person <|- Person
.BaseClass <|-- Person
net.dummy.Meeting o-- Person
}
BaseClass <|-- net.unused.Person
```

title `uml模型图题目支持MD`
center header
`在此处添加标头`
endheader

'******* 类声明模块 *******'
'类型:class,abstract,interface,annotation,enum'
'访问域:(-)私有,(#)保护,(~)包私有,(+)公有'

class `类名`<`扩展对象`> as `类别名`{
-- `属性组名` -- '分隔符--,..,==,__'
`访问域修饰符` `static/abstract` `属性名称`:`类型` = `值1`
__ `函数组名` __
`访问域修饰符` func `函数名称`(`参数1`:`类型`,`参数2`:`类型`)
}
'显示/隐藏类,类方法属性等 关键字支持class,interface,enum'
hide `类名/方法名`

'---- 声明类关系线备注,可以当做用例来参与到关系连接中 ---'
note "`备注内容`" as `备注对象`

'多行备注对象'
note as `备注对象`
"`备注内容`"
end note

'###### 类备注模块 类声明末尾使用:note 位置: 备注#########'
note `left/right/top/bottom` of `object` #`颜色`
`支持markdown语法加粗／斜体／删除线／下划线／波浪下划线 和HTML`
end note

'&&&&&& 类组合模块 类模块 &&&&&&&'
'六种组合样式:Node,Rectangle,Folder,Frame,Cloud,Database'
scale  `750` `width/height`
package `module名` <<`模块样式`>> `#背景色`{
class `类名`<`扩展对象`> as `类别名`{
-- `属性组名` -- '分隔符--,..,==,__'
`访问域修饰符` `static/abstract` `属性名称`:`类型` = `值1`
__ `函数组名` __
`访问域修饰符` func `函数名称`(`参数1`:`类型`,`参数2`:`类型`)
}
}

'@@@@@@@ 命名空间模块 关系模块  @@@@@@@@'
namespace `com.cn` #`空间背景色`{

'关系节点符:(|>)继承,(*)合成 ,(o)聚合, 其他#,x,},+,^ 连线符:(--)实线 ，(..)虚线'
`类名/包名`"`基数`" `节点符` `left/right..``[#线色]`-`节点符` "`基数`"`类名/包名`:`消息` >
note `left/right/top/bottom` on link #`颜色`
`连接注释体`
end note}

'>>>>>> 类关系图及连接备注模块 >>>>>>>>'
'关系节点符:(|>)继承,(*)合成 ,(o)聚合, 其他#,x,},+,^ 连线符:(--)实线 ，(..)虚线'
`类名/包名`"`基数`" `节点符` `left/right..``[#线色]`-`节点符` "`基数`"`类名/包名`:`消息` >
note `left/right/top/bottom` on link #`颜色`
`连接注释体`
end note






center footer
`在此处添加脚注`
endfooter
