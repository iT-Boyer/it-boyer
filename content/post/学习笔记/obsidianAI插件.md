+++
title = "制作 Text Generator Plugin 的模板"
description = "博客简介"
date = 2023-05-28T23:57:00+08:00
lastmod = 2023-05-28T23:59:28+08:00
tags = ["Obsidian", "翻译"]
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## 使用 create Template 命令创建模板 {#使用-create-template-命令创建模板}

使用此命令,您可以从活动文档创建模板。 **默认热键** : `Alt + C` 。 

-   默认情况下,模板将在 `local` 文件夹中创建 `Templates Folder` ([模板](https://text-gen.com/templates))(您可以随时更改路径)。
-   模板必须在 `Templates Folder` 中。


## 从零开始创建模板 {#从零开始创建模板}

-   在 `Templates Folder` 中创建文件。
-   添加[模板文件元数据](https://text-gen.com/template-file-metadata)。


### 模板元数据 {#模板元数据}

```text
---
PromptInfo:
 promptId: getParagraph
 name: ✍️ Write paragraphs
 description: select a content contiens items, a paragraph for each item will be generated.
 required_values: title, outline
 author: Noureddine
 tags: writing
 version: 0.0.1
 commands:
 - generate
---
```

`PromptInfo` 包含有关提示的信息,必须在每个模板的头部。它包含以下字段: 

-   `promptId`:标识提示的唯一字符串。它用于UI中每个提示的ID。
-   `name`:识别提示的名称。它应该简短和精确。
-   `description`:解释此提示的作用和使用方法的描述,您可以在此处放任何内容,但简单和简洁更好。
-   `author`:要归功于编写此提示的作者姓名或用户名。
-   `required_values`:模板所需的值。
-   `tags`:描述此提示的一些标签。此信息用于过滤提示。
-   `version`:此提示的版本,遵循[语义版本控制](%5Bhttps://semver.org/%5D(https://semver.org/))。
-   `commands`:用于将模板转换为命令,以便为每个命令分配热键(您需要重新加载插件以查看更改) 
    -   `generate`:编译模板并通过 gpt-3 生成内容,将结果插入 `活动笔记` 中。
    -   `generate&create`:编译模板并通过 gpt-3 生成内容,将结果插入 `新建笔记` 中。
    -   `insert`:编译模板并将结果插入活动笔记中,而无需 gpt-3 将结果插入 `活动笔记` 中。
    -   `insert&create`:编译模板并将结果插入活动笔记中,而无需 gpt-3 将结果插入 `新建笔记` 中。
    -   `model`:将模板显示为输入形式,并将结果插入 `活动笔记` 中。
    -   `clipboard`:编译模板并通过 gpt-3 生成内容,将结果写入剪贴板。


## 构建模板 {#构建模板}

-   使用[模板可用上下文](https://text-gen.com/context-available-for-templates)准备模板。有关模板引擎的更多信息,请参见[handlebarsjs](https://handlebarsjs.com/)。
-   模板可以通过[模板命令](https://text-gen.com/commands)访问。


### **可用于模板的上下文** {#可用于模板的上下文}

可用于模板的上下文 **模板引擎** 有关模板引擎的更多信息,请参见[handlebarsjs](https://handlebarsjs.com/) **模板引擎** 包含的信息在插件设置中是可选的。请参阅[考虑的上下文](https://text-gen.com/considered-context)。 

1.  `title`: 是文档的标题 。
2.  `selection`: 是根据[考虑的上下文](https://text-gen.com/considered-context)中解释的方法选择的文本。
3.  `selection`:选择(新的v0.2.17),它是一个数组,包含所有选择。您可以通过 `selections` 访问它。
4.  `context`:上下文 包括YAML变量,Title,StaredHeadings,Selection
5.  `frontmatter`:frontmatter 变量可以直接使用或在 `frontmatter` 变量(对象)中使用。
6.  `headings`: 它是一个对象,包含所有headings及其内容。
7.  `children`: 它是一个数组,包含引用的笔记。您可以通过=children=访问它。
8.  `mentions`: `mention` 是一个对象,包含两个数组:/linked/和/unlinked/。每个数组包含文档提到的段落。
9.  `highlights`:突出显示,它是一个数组,包含 obsidian 突出显示。您可以通过 `highlights` 访问它。
10. `extractions`:提取(v0.3.1) `extractions` 是一个对象,包含三个数组: 
    -   _PDFExtractor_ 是一个数组,包含笔记中嵌入的PDF文档的文本格式。
    -   _WebPageExtractor_ 是一个数组,包含笔记中的所有外部链接的内容。
    -   _YoutubeExtractor_ 是一个数组,包含所有 YouTube 视频转录的内容。
    -   _AudioExtractor_ 是一个数组,包含通过 Whisper OpenAI 处理的所有音频笔记的转录(文件大小限制:25MB)。


## **Considered Context** 考虑的上下文 {#considered-context-考虑的上下文}

`Transformer` 需要 _context_ 来生成准确的文本。所以我们将与 `prompt` 一起发送的所有数据,我们称之为 `Considered Context` 。 


### 使用基本命令 {#使用基本命令}

请参阅 <https://text-gen.com/commands.html> 中的基本命令。 


### 使用 selection {#使用-selection}

它可以是: 

-   所选文本。
-   如果光标所在的行/不为空/时,为当前行的文本。
-   如果/行为空/,为文档的全部内容。


### 添加文档标题 {#添加文档标题}

您可以通过启用插件设置来为 **Considered Context** 添加 `title` 。 


### 添加星号块 {#添加星号块}

如果您想在长篇文章中包含特定部分,可以通过在标题结尾添加星号 `*` 来添加标题内容。 

-   您需要在设置中启用该选项。
-   例如

<!--listend-->

```text
# Title*
This is a title
# Introduction
write introduction
```


### 添加元数据变量 {#添加元数据变量}

您可以在元数据或 `frontmatter` 变量中添加任何您想要的信息(这将在 `生成文本(带元数据)` 中考虑。您必须避免插件使用的 `YAML关键词` 。例如: 

```text
---
title: Considered Context
tags: doc
layout: note
---
```


### 使用模板命令 {#使用模板命令}

从版本0.1.0开始,/文本生成器插件/支持模板。确保模板上提供的信息可供您在插件设置中使用 

