---
title: "gptel-agent 代理模式使用指南"
description: "博客简介"
date: 2025-12-10T21:31:00+08:00
lastmod: 2025-12-10T21:34:36+08:00
categories: ["Inbox"]
draft: false
authors: ["iTBoyer"]
cover: "https://mmbiz.qpic.cn/mmbiz_jpg/v5Lr5bLDWhxyvgAR84nTjovnGSuhiaicCibJLibL317icK6kkhLpo5YKzqUU8libC3CpwWmBCgwhscfF48W3okaB3c6A/640?wx_fmt=jpeg"
---

<div class="ox-hugo-toc toc">

<div class="heading">Table of Contents</div>

- [安装 gptel-agent](#安装-gptel-agent)
- [基本使用方法](#基本使用方法)
    - [在专用会话中使用：](#在专用会话中使用)
    - [在 Emacs 任何地方使用：](#在-emacs-任何地方使用)
- [可用的工具](#可用的工具)
- [预设说明](#预设说明)
- [子代理使用](#子代理使用)
    - [内置子代理：](#内置子代理)
    - [使用方法：](#使用方法)
- [自定义子代理](#自定义子代理)
    - [从 Markdown 文件创建：](#从-markdown-文件创建)
    - [从 Org 文件创建：](#从-org-文件创建)
- [重要注意事项](#重要注意事项)

</div>
<!--endtoc-->

<style>
.org-todo {
    font-size: 0.8em;
    font-weight: 700;
}
/* *** Org TODO set to TODO state */
.org-todo.todo {
    color: #e60000;
}
/* *** Org TODO set to DONE state */
.org-todo.done {
    color: green;
}

.org-todo.cancel {
    color: blue;
}
</style>

<section class="outline-1 foo">

## 安装 gptel-agent {#安装-gptel-agent}

**方法1：通过 MELPA** 

```elisp
M-x package-install ⏎ gptel-agent
```

**方法2：使用 use-package 配置** 

```elisp
(use-package gptel-agent
  :straight t
  :config (gptel-agent-update))  ; 从 agents 目录读取文件
```

**方法3：手动安装** 克隆仓库后运行 `M-x package-install-file` 选择仓库目录 

</section>

<section class="outline-1 foo">

## 基本使用方法 {#基本使用方法}

<section class="outline-2 foo">

### 在专用会话中使用： {#在专用会话中使用}

1.  \*启动代理模式\*：运行 `M-x gptel-agent`
2.  \*使用前缀参数\*：使用 `C-u M-x gptel-agent` 获取更多选项
3.  \*正常使用 gptel\*：调用 `gptel-send` 等命令
4.  \*重置配置\*：如果更改了系统提示词、工具或其他设置，可以从 gptel 菜单重新应用 "gptel-agent" 预设

</section>

<section class="outline-2 foo">

### 在 Emacs 任何地方使用： {#在-emacs-任何地方使用}

1.  \*更新配置\*：在配置中添加 `(gptel-agent-update)`
2.  \*应用预设\*：在任意缓冲区中从 gptel 菜单应用 "gptel-agent" 预设
3.  \*或直接使用\*：在提示词中包含 =@gptel-agent=，gptel 会自动使用代理能力

</section>

</section>

<section class="outline-1 foo">

## 可用的工具 {#可用的工具}

gptel-agent 提供以下工具访问： 

-   \*网络\*：基础网页搜索、URL 获取、YouTube 视频元数据
-   \*本地文件\*：读写编辑本地文件
-   \*Emacs 状态\*：文档查找和 Elisp 代码执行
-   \*Bash\*：在 POSIX 环境中运行

</section>

<section class="outline-1 foo">

## 预设说明 {#预设说明}

gptel-agent 包含两个主要预设： 

1.  \*gptel-agent\*：完整代理能力，可执行各种任务
2.  \*gptel-plan\*：规划预设，只有只读文件系统访问权限，专注于创建系统性行动计划

</section>

<section class="outline-1 foo">

## 子代理使用 {#子代理使用}

gptel-agent 支持使用子代理来委托任务： 

<section class="outline-2 foo">

### 内置子代理： {#内置子代理}

-   \*executor\*：自主、非交互式工作的 LLM 副本
-   \*researcher\*：只读权限，可搜索网络或本地文件
-   \*introspector\*：检查 Emacs 状态，访问或搜索文档

</section>

<section class="outline-2 foo">

### 使用方法： {#使用方法}

-   \*自动委托\*：LLM 会根据需要自动将任务委托给合适的子代理
-   **显式请求\*：直接在提示词中指定使用特定子代理，例如："\*使用 researcher 查找...**"

</section>

</section>

<section class="outline-1 foo">

## 自定义子代理 {#自定义子代理}

<section class="outline-2 foo">

### 从 Markdown 文件创建： {#从-markdown-文件创建}

在 `gptel-agent-dirs` 目录中创建包含 YAML 前言的 Markdown 文件： 

```markdown
---
name: arxiv-explorer
description: 搜索 ArXiv 并解析结果
tools: [mcp-arxiv, WebSearch]
pre: (lambda () (gptel-mcp-connect '(arxiv) 'sync))
---

你是专门的研究代理，可以访问 ArXiv...
```

</section>

<section class="outline-2 foo">

### 从 Org 文件创建： {#从-org-文件创建}

在 Org 文件顶部添加属性块： 

```org
:PROPERTIES:
:name: arxiv-explorer
:description: 搜索 ArXiv 并解析结果
tools: mcp-arxiv WebSearch
pre: (lambda () (gptel-mcp-connect '(arxiv) 'sync))
:END:

你是专门的研究代理，可以访问 ArXiv...
```

</section>

</section>

<section class="outline-1 foo">

## 重要注意事项 {#重要注意事项}

-   \*确保 gptel 更新\*：不要只使用最新的稳定版本
-   \*设置模型和后端\*：配置 `gptel-model` 和 `gptel-backend` 使用你偏好的 LLM
-   \*token 消耗\*：gptel-agent 使用的 token 比普通 gptel 交互要多得多

来源：[gptel-agent GitHub 仓库](<https://github.com/karthink/gptel-agent>) 

--- 

总结一下，gptel-agent 是一个强大的代理模式扩展，让你能在 Emacs 中创建和使用 AI 代理，支持任务委托、工具使用和各种自动化功能。

</section>

