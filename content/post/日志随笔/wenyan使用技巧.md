---
title: "今天日志3"
description: "博客简介"
date: 2025-09-07T06:22:00+08:00
draft: false
authors: ["iTBoyer"]
cover: "https://mmbiz.qpic.cn/mmbiz_jpg/v5Lr5bLDWhxyvgAR84nTjovnGSuhiaicCibJLibL317icK6kkhLpo5YKzqUU8libC3CpwWmBCgwhscfF48W3okaB3c6A/640?wx_fmt=jpeg"
---

<div class="ox-hugo-toc toc">

<div class="heading">Table of Contents</div>

- [**2. 使用 `wenyan-mcp` 发布公众号文章**](#2-dot-使用-wenyan-mcp-发布公众号文章)
- [**3. 整合到 Emacs 工作流**](#3-dot-整合到-emacs-工作流)
- [**4. 已知问题与解决方案**](#4-dot-已知问题与解决方案)
- [**5. 参考资源**](#5-dot-参考资源)

</div>
<!--endtoc-->

![title](https://babyno.top/imgs/posts/2025-06-05-let-ai-help-you-manage-your-gzh-layout-and-publishing/7.jpg) 

<section class="outline-1 foo">

## **2. 使用 `wenyan-mcp` 发布公众号文章** {#2-dot-使用-wenyan-mcp-发布公众号文章}

-   \*目标\*：将 Markdown 文件通过 `wenyan-mcp` 格式化并发布到微信公众号草稿箱。
-   \*步骤\*： 
    1.  \*安装 =wenyan-mcp=\*： 
        -   使用 `npx` 或 Docker 方式部署 `wenyan-mcp` 工具。
        -   示例（Docker）： 
            ```bash
                          docker run -v $(pwd):/app -w /app wenyan/mcp publish <your-file>.md
            ```
    2.  \*配置 MCP 工具\*： 
        -   确保 `wenyan-mcp` 的配置文件（如 `mcp.config.js=）中包含公众号的 =appid` 和 =secret=。
    3.  \*发布文章\*： 
        -   使用命令行工具或脚本调用 `wenyan-mcp` 的发布功能。
        -   示例： 
            ```bash
                          npx wenyan-mcp publish <your-file>.md
            ```

--- 

</section>

<section class="outline-1 foo">

## **3. 整合到 Emacs 工作流** {#3-dot-整合到-emacs-工作流}

-   \*自动化脚本\*： 
    1.  编写 Emacs Lisp 函数，将 Org 文件导出为 Markdown 并调用 `wenyan-mcp` 发布。 
        ```elisp
                  (defun publish-to-wechat ()
                    (interactive)
                    (org-md-export-to-md)
                    (shell-command "npx wenyan-mcp publish *.md"))
        ```
    2.  绑定快捷键或添加到 Org 模板中，实现一键发布。

-   \*调试与优化\*： 
    -   如果 `npx` 方式无法上传图片，尝试使用 Docker 部署工具。
    -   确保图片路径在 Markdown 中正确引用，或使用 `wenyan-mcp` 的图片上传功能。

--- 

</section>

<section class="outline-1 foo">

## **4. 已知问题与解决方案** {#4-dot-已知问题与解决方案}

| 问题         | 解决方案                                       |
|------------|--------------------------------------------|
| `npx` 无法上传图片 | 使用 Docker 方式部署工具                       |
| 图片路径错误 | 检查 Markdown 文件中的图片链接是否为绝对路径或符合公众号要求 |
| MCP 配置缺失 | 确保 `mcp.config.js` 包含正确的公众号 `appid` 和 `secret` |

--- 

</section>

<section class="outline-1 foo">

## **5. 参考资源** {#5-dot-参考资源}

-   [\*=wenyan-mcp= GitHub 仓库\*](<https://github.com/wenyan-mcp/wenyan-mcp>)：工具的官方文档和示例。
-   [\*Org 模式导出指南\*](<https://orgmode.org/manual/Exporting.html>)：了解如何导出 Org 文件。
-   [\*Emacs Lisp 脚本编写\*](<https://www.gnu.org/software/emacs/manual/elisp.html>)：自定义 Emacs 工作流。

--- 

通过以上步骤，您可以在 Emacs 中高效地将 Org 文件转换为 Markdown 并使用 `wenyan-mcp` 发布到微信公众号。是否需要进一步的帮助，例如编写具体的 Emacs 脚本或调试 Docker 部署？ 

</section>

