---
title: flowchart流程图语法
date: 2017-06-24 15:32:41
updated: 2017-06-24 22:24:54
comments: true
tags: [UML]
categories: 
- 博客站务
keywords: 
permalink: 
---
## 基本格式
http://blog.csdn.net/KimBing/article/details/52934959?locationNum=2&fps=1
### 对象
1. 六种对象：开始对象，结束对象，操作对象，条件对象，输入对象，子任务对象
```
对象变量=>[start|end|operation|condition|inputoutput|subroutine]:[描述信息]
```
2. 流程控制语法
流程控制三要素：位置、方向，条件
```
条件对象(YES/NO/消息)-> 其他对象
操作对象(left/right/top)-> 其他对象

```

样例
```
//结构模块
st=>start: 开始
e=>end: 结束
op=>operation: 我的操作
cond=>condition: 确认？

//流程控制模块
st->op->cond
cond(yes)->e
cond(no)->op
```
效果：
```flow
//结构模块
st=>start: 开始
e=>end: 结束
op=>operation: 我的操作
cond=>condition: 确认？

//流程模块
st->op->cond
cond(yes)->e
cond(no)->op
```
