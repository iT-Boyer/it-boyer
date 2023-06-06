+++
title = "介绍 org-ql 常用命令"
description = "博客简介"
date = 2023-05-20T09:54:00+08:00
lastmod = 2023-05-20T11:03:20+08:00
tags = ["翻译"]
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## org-ql-find {#org-ql-find}

使用 emacs 的内置补全工具和一个 `org-ql` 查询选择的标题实现跳转: 

-   `org-ql-find` 在当前缓冲区中搜索。
-   `org-ql-find-in-agenda` searches in `(org-agenda-files)`.
-   `org-ql-find-in-org-directory` searches in `org-directory`.

<style>.foo img { border:2px solid black; }</style>

{{< figure src="https://raw.githubusercontent.com/alphapapa/org-ql/master/images/org-ql-find.png" alt="Org mode logo" >}} 


## org-ql-refile {#org-ql-refile}

此命令将当前的 org 条目重新归档到通过使用 `org-ql` 补全进行搜索所选择的条目中。它搜索 `org-refile-targets` 中列出的文件以及当前缓冲区。 


## org-ql-search {#org-ql-search}

读 `QUERY` 和用 `org-ql` 搜索 。以交互方式提示输入这些变量: 

`BUFFERS-FILES`: `A` 搜索 一个 buffer 列表/文件列表。互动，也支持是: 

-   `buffer`: 搜索当前缓冲区
-   `all`: 搜索所有组织缓冲区
-   `agenda`: 搜索函数 `org-agenda-files` 返回的缓冲区列表
-   是一个用空格分隔的文件或缓冲区名称的列表.

`GROUPS`: 设置一个 `org-super-agenda` 分组 .  参见 `org-super-agenda-groups`. 

`NARROW`: 当非nil时，在搜索之前不要展开缓冲区。交互式的，带前缀，留窄。 

`SORT`:一个或一组 `org-ql` 排序函数，如 `date` 或 `priority` 。 

**快捷键:** 键绑定在结果缓冲区中 

-   `r`: 刷新结果。带前缀，提示调整搜索参数。
-   `v`: 展示 `transient` view dispatcher(像 magit 的弹出窗口)。
-   `C-x C-s`: 将查询保存到变量 `org-ql-views` (可通过命令 `org-ql-view` 访问)。

**Note:** 视图缓冲区当前处于 `org-agenda-mode` ，这意味着 _some_  Org Agenda 命令可以工作，例如跳转到 entry 和更改 item 优先级(不必更新视图)。此功能是实验性的，不能保证对所有命令都能正确工作。(它之所以有效，是因为在每个项目上放置了适当的文本属性，模仿议程缓冲区。) 


## helm-org-ql {#helm-org-ql}

该命令显示与helm的匹配。 

-   在helm会话中按 `C-x C-s` 将结果保存到 `org-ql-search` 缓冲区中。


## org-ql-view {#org-ql-view}

选择并显示存储在 `org-ql-views` 中的视图。 

**Bindings:** buffer 已绑定的快捷键说明 

-   `g`, `r`: Refresh results.  With prefix, prompt to adjust search parameters.
-   `v`: Show `transient` view dispatcher (like Magit's popups).
-   `C-x C-s`: 将查询保存到变量 `org-ql-views` (可通过命令 `org-ql-view` 访问)。


## org-ql-view-sidebar {#org-ql-view-sidebar}

显示一个侧边栏窗口，列出存储在 `org-ql-views` 中的视图，以便于访问。在侧边栏中，按 `RET` 或 `mouse-1` 显示该点的视图，并按 `c` 自定义该点的视图。 


## org-ql-view-recent-items {#org-ql-view-recent-items}

显示 `FILES` 中最近 `DAYS` 天的项目，时间戳为 `TYPE` 。 

`TYPE` 支持这些类型： `ts`, `ts-active`, `ts-inactive`, `clocked`, `closed`, `deadline`, `planning`, or `scheduled`. 

`FILES` 默认为函数 `org-agenda-files` 返回的文件。 


## org-ql-sparse-tree {#org-ql-sparse-tree}

参数: `(query &key keep-previous (buffer (current-buffer)))` 

在 `BUFFER` 中显示 `QUERY` 的稀疏树，并返回结果的个数。该树将显示查询匹配的行，以及在 `org-show-context-detail` 中定义的任何其他上下文，如所示。 

`QUERY` 是一个 `org-ql` 查询 sexp(引号，因为这是一个函数)。 `BUFFER` 默认为当前缓冲区。当 `KEEP-PREVIOUS` 为 non-nil 时(在查找匹配项之前)，大纲不会重置为概述状态，这允许对该命令进行嵌套调用。在生成稀疏树后运行 `org-occur-hook` 。 

