+++
title = "org-roam新手入门"
description = "博客简介"
date = 2023-06-04T19:01:00+08:00
tags = ["翻译", "org-roam"]
categories = ["学习笔记"]
draft = false
creator = "Emacs 30.0.50 (Org mode 9.6.1 + ox-hugo)"
authors = ["iTBoyer"]
password = ""
+++

## The Org-roam Node {#the-org-roam-node}

在使用手册时,我们首先介绍一些术语。我们将 Org-roam 中的基本单位称为节点。我们将节点定义如下: 

> 一个节点是任何具有ID的标题或顶级文件。 

例如,使用此示例文件内容: 

```org
  :PROPERTIES:
  :ID:       foo
  :END:
  #+title: Foo

  * Bar
  :PROPERTIES:
  :ID:       bar
  :END:
```

我们创建两个节点: 

1.  一个文件节点“Foo”,ID为 ~foo~。

2.  一个标题节点“Bar”,ID为 ~bar~。

没有 ID 的标题将不被视为 Org-roam 节点。可以通过交互命令 `M-x org-id-get-create` 向文件或标题添加 Org ID。 

接着,我们将文件内容分解为节点层次结构,称为 Org-roam 网络。每个节点都链接到其父节点和子节点。例如,上述示例的网络结构为: 

\`\`\` 

-   Foo 
    -   Bar

\`\`\` 

其中: 

-   `Foo` 是文件节点,ID为 `foo`,链接到子节点 `Bar` 。
-   `Bar` 是标题节点,ID为 `bar`,链接到父节点 `Foo` 。

网络的根节点为文件节点。文件节点可以链接到其他文件节点,构建更大的网络。网络提供一种视觉表示,用于在头脑中构建和探索思想之间的关联。 

接下来,我们将更详细地探讨节点类型和网络。我们还将看到 Org-roam 如何帮助构建和维护此网络。 


## Links between Nodes {#links-between-nodes}

我们使用 Org 的标准ID链接(例如 `id:foo`)在节点之间建立链接。在计算节点之间的链接时,只考虑ID链接,但 Org-roam 会缓存文档中的所有其他链接以供外部使用。 

例如,给定文件: 

```org
  :PROPERTIES:
  :ID:       foo
  :END:
  * [[http://example.com][Example]]
  * Bar
  :PROPERTIES:
  :ID:       bar
  :END:
```

其网络为: 

\`\`\` 

-   Foo 
    -   Bar

\`\`\` 

其中, Org-roam 忽略链接 [Example](http://example.com) , 但会在 Org-roam 缓存中记录该链接,以供外部使用。只有ID链接 `id:bar` 会用于计算网络结构。 

通过只使用ID链接来构建网络,Org-roam 可以跟踪节点之间的父子关系,并构建层次结构。如果考虑所有的链接,网络可能变得杂乱和难以理解。但是,通过缓存其他链接,Org-roam 仍然允许复杂的跨文档链接,这些链接可供外部工具和进程使用。 

此外,Org-roam 网络是有向无环图(DAG)。这意味着: 

1.  网络中的每个节点至多有一个父节点。这避免了在网络中形成"环"。

2.  网络可以由多个根节点组成。文件节点始终是网络中的根节点。

3.  网络中的路径可以是单向的,也可以是双向的。双向路径通过在两个节点之间建立双向链接来实现。

理解 Org-roam 网络的这些属性对于在 Org-roam 中有效导航和组织信息至关重要。 


## Setting up Org-roam {#setting-up-org-roam}

Org-roam 的功能源于其积极的缓存:它爬取 `org-roam-directory` 中的所有文件,并维护所有链接和节点的缓存。 

要开始使用 Org-roam,请选择一个位置存储 Org-roam 文件。包含您笔记的目录由变量 `org-roam-directory` 指定。Org-roam 在 `org-roam-directory` 中递归搜索笔记。在调用 Org-roam 函数之前,需要设置此变量。 

对于本教程,创建一个空目录,并设置 `org-roam-directory`: 

```emacs-lisp
(make-directory "~/org-roam")
(setq org-roam-directory (file-truename "~/org-roam"))
```

`file-truename` 函数仅在 `org-roam-directory` 内使用符号链接时必要:Org-roam 不解析符号链接。但是,可以指示 Emacs 始终解析符号链接,代价是性能损失: 

```emacs-lisp
(setq find-file-visit-truename t)
```

接下来,我们设置 Org-roam 以在文件更改时运行函数以维持缓存一致性。这通过运行 `M-x org-roam-db-autosync-mode` 来实现。要确保 Org-Roam 在启动时可用,请将此内容放入 Emacs 配置中: 

```emacs-lisp
(org-roam-db-autosync-mode)
```

要手动构建缓存,请运行 `M-x org-roam-db-sync` 。第一次构建缓存可能需要一段时间,但后续构建通常是瞬间的,因为它们只重新处理已修改的文件。 

org-roam-directory 是您存储 Org-roam 文件的位置。设置 org-roam-directory 是使用 Org-roam 的第一步。 

org-roam 通过运行 org-roam-db-autosync-mode 来保持缓存与文件系统同步。这会在保存 Org-roam 文件时同步缓存。 

org-roam-db-sync 手动构建缓存。首次构建缓存需要较长时间,但后续构建通常很快,因为它们只需要重新处理已修改的文件。 

理解 Org-roam 的缓存机制对于高效使用 Org-roam 至关重要。缓存为许多 Org-roam 功能提供支持,如果缓存过期,这些功能的性能和正确性可能会受到影响。 


## 创建和链接节点 {#创建和链接节点}

Org-roam 使创建笔记并将其链接在一起变得容易。有2个主要函数用于创建节点: 

-   `org-roam-node-insert`:如果不存在则创建一个节点,并在点处插入到节点的链接。
-   `org-roam-node-find`:如果不存在则创建一个节点,并访问该节点。
-   `org-roam-capture`:如果不存在则创建一个节点,并在完成后恢复当前窗口配置。

首先尝试 `org-roam-node-find` 。调用 `M-x org-roam-node-find` 将显示 `org-roam-directory` 中节点的标题列表。目前应该什么也不显示,因为目录中没有笔记。输入要创建的笔记的标题,然后按 `RET` 。这将启动笔记创建过程。此过程使用 `org-capture` 的模板系统,可以定制(见 **The Templating System** )。使用默认模板,按 `C-c C-c` 完成笔记捕获。 

现在我们有一个节点,可以使用 `M-x org-roam-node-insert` 插入到节点的链接。这会带出节点列表,其中应包含您刚刚创建的节点。选择节点将插入到节点的 `id:` 链接。如果您输入的标题不存在,您将再次通过节点创建过程。 

也可以方便地通过 Org-roam 提供的 `completion-at-point` 功能插入链接(见 **Completion 补全** )。 

`org-roam-node-insert` 和 `org-roam-node-find` 是创建新节点的主要方法。这些命令将引导您通过节点创建过程,要么插入到现有节点的链接,要么创建新节点。理解这些命令使您能够快速而方便地构建网络。 

使用 `completion-at-point` 功能,可以轻松地通过键入节点标题或标签来插入链接,这不需要显示节点列表。这使链接到现有节点变得非常高效。 

创建新节点和手动将它们链接在一起是构建 Org-roam 网络的基础。熟悉这些功能将使您在使用 Org-roam 中变得更加自如。 


## 自定义节点补全 {#自定义节点补全}

节点选择是通过 `completing-read` 接口实现的,通常通过 `org-roam-node-read` 。这些节点的呈现由 `org-roam-node-display-template` 管理。 

-   变量: `org-roam-node-display-template` 配置 Org-roam 节点的显示格式。 
    
    `${field-name:length}` 形式的模式根据当前节点进行插值。 
    
    每个 `field-name` 都由 org-roam-node 的每个对应访问器函数的返回值替换,例如 `${title}` 将由 `org-roam-node-title` 的结果插值。您也可以使用 cl-defmethod 定义自定义访问器。例如,您可以定义: 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
      (cl-defmethod org-roam-node-my-title ((node org-roam-node))
            (concat "My " (org-roam-node-title node)))
    ```
    然后在这里或在捕获模板中将其引用为 `${my-title}` 。 
    
    `length` 是可选的指定符,声明可以使用多少个字符来显示对应字段的值。如果未指定,该字段将原样插入,即它不会对齐或修剪。如果它是一个整数,则该字段将相应地对齐,所有超出的字符将被修剪。如果它是 “\*”,则该字段将尽可能使用许多字符,并将相应地对齐。 
    
    这个变量也可以分配一个闭包,在这种情况下,闭包被求值,其返回值被用作模板。闭包必须求值为有效的模板字符串。

如果您使用垂直完成框架,例如 Ivy 和 Selectrum ,Org-roam 支持生成对齐的表格式完成界面。例如,要包括最多10个字符宽的标签列, 可以将 `org-roam-node-display-template` 设置如下: 

```emacs-lisp
(setq org-roam-node-display-template
      (concat "${title:*} "
              (propertize "${tags:10}" 'face 'org-tag)))
```

这将产生类似于: 

```text
My Title         todo
Another Note    blog
```

的完成接口,其中 Title 被对齐,标签被修剪到10个字符。 

理解 `org-roam-node-display-template` 对于定制 Org-roam 节点选择界面至关重要。通过调整此变量,您可以生成对您的工作流最有意义的界面。 

