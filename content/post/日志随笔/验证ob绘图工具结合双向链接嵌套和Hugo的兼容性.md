  * [ ] +++
title = "验证ob绘图工具结合双向链接嵌套和Hugo的兼容性"
description = "ob中有很不错的插件，可以实现沉浸式编写博客，现在有个绘图工具，尝试使用ob的嵌套图片和md嵌套两种方式，在Hugo中的效果"
date = "2022-10-20 20:53:13"
publishDate = "2022-10-20"
lastmod = "2022-10-20 20:53:13"
categories = ["日志随笔"]
draft = false
authors = ["iTBoyer"]
+++

### 使用ob语法添加图片预览
```
!\[\[Drawing2022-10-2020.43.21.excalidraw.png\]\]
```
![2022-10-2020.43.21.excalidraw.png](/Excalidraw/Drawing2022-10-2020.43.21.excalidraw.png)

### 使用markdown语法嵌套图片预览
```
![](Excalidraw/Drawing2022-10-2020.43.21.excalidraw.png)
```
![](Excalidraw/Drawing2022-10-2020.43.21.excalidraw.png)

### 解决双向链接的问题

使用hugo端代码和shell方式配合，在githubworkfow的过程，执行上述操作，实现在发布时做一次双向链接的翻译成支持hugo的格式。

https://zhuanlan.zhihu.com/p/351240588?utm_id=0

https://matnoble.me/tech/hugo/shortcodes-practice-tutorial-for-hugo/

`Hugo`无法解析`Obsidian`的`双向链接`，解决这个问题是本文的目标。  
  
【解决方案】 我通过`shell脚本`和`Hugo短代码`的配合解决这个问题。 通过`shell脚本`将`Obsidian`的`双向链接`转换成`Hugo`可以识别的自定义格式；`Hugo短代码`则负责解析这种自定义格式。

1. roamlink无效的问题
[ob双向链接替换为roamlink之后，hugo server预览看不到效果？](https://github.com/chaoskey/notes/issues/1)

暂时使用直接替换的处理方法, `仅支持替换Drawing目录下的图片`：
```sh
#图片链接变换
sed -i 's/\[\[Drawing\([^[]*\)|\([^[]*\)\]\]/[\2](\/Excalidraw\/Drawing\1)/g; s/\[\[Drawing\([^[|]*\)\]\]/[\1](\/Excalidraw\/Drawing\1)/g; ' $1"/"$file
```

1. sed修改文件内容时，语法在mac和linux的用法区别
```shell
#linux
sed -i '替换语' file 
# mac 终端测试命令, 修改文件时，要有空字符分割。
sed -i '' '替换语' file
```
