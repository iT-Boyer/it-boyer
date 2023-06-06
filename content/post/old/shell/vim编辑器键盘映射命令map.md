---
title: vim编辑器键盘映射命令map
date: 2017-07-04 15:06:52
updated: 2018-09-22 21:18:50
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
---
使用`:map`命令，可以将键盘上的某个按键与Vim的命令绑定起来。
例如使用以下命令，可以通过F5键将单词用花括号括起来：
```
:map <F5> i{e<Esc>a}<Esc>
```
执行过程：`i{`将插入字符`{`，然后使用`Esc`退回到命令状态；接着用`e`移到单词结尾，`a}`增加字符`}`，最后退至命令状态。
在执行以上命令之后，光标定位在一个单词上（例如amount），按下F5键，这时字符就会变成{amount}的形式。

## 不同模式下的键盘映射
使用下表中不同形式的map命令，可以针对特定的模式设置键盘映射：

|Command 命令|Normal 常规模式  |Visual可视化模式|Operator Pending运算符模式|Insert Only插入模式|Command Line 命令行模式|
|:-------:|:-------:|:--------:|:-------:|:-------:|:--------:|
|:map |y |y| y ||  |
|:nmap |y|  ||  ||  |
|:vmap || y ||  | |
|:omap ||  |y|  |  |
|:map! ||  || y |y |
|:imap ||  || y ||
|:cmap ||  ||  |y|

### 查看键盘映射
```
:map
```
### 取消键盘映射
```
:unmap <F10> #参数
```
注意：必须为:unmap命令指定一个参数。如果未指定任何参数，那么系统将会报错，而不会取消所有的键盘映射。
针对不同模式下的键盘映射，需要使用与其相对应的unmap命令。例如：使用:iunmap命令，取消插入模式下的键盘映射；而取消常规模式下的键盘映射，则需要使用:nunmap命令。
如果想要取消所有映射，可以使用:mapclear命令。请注意，这个命令将会移除所有用户定义和系统默认的键盘映射。

### 参考文章
[VIM键盘映射 (Map)](http://blog.csdn.net/daofengdeba/article/details/7605124)
[Vim按键映射](http://www.pythonclub.org/vim/map-basic)

