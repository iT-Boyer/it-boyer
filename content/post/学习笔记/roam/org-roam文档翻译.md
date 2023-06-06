+++
title = "org-roam文档翻译"
description = "博客简介"
date = 2023-06-04T17:49:00+08:00
draft = false
creator = "Emacs 30.0.50 (Org mode 9.6.1 + ox-hugo)"
authors = ["iTBoyer"]
password = ""
+++

Org-roam 是一个用于联想思维的工具。它在[Org mode](https://orgmode.org/)中模拟[Roam Research](https://roamresearch.com/) 的一些关键功能。 

Org-roam 允许轻松地进行非层次化的记笔记:使用 Org-roam,笔记会自然流畅地记录,这使得记笔记变得有趣和容易。Org-roam 增强了 Org mode 语法,任何已经使用 Org mode 进行个人知识库的人都可以使用它。 

Org-roam 利用 Org mode 周围成熟的生态系统。例如,它对[org-ref](https://github.com/jkitchin/org-ref)提供首要支持以进行引文管理,并能够利用 Org 的优秀 LaTeX 和源码块评估功能。 

相比其他工具,Org-roam 提供以下优势: 

`隐私和安全性:` 你的个人知识库只属于你,完全离线和由你控制。使用 GPG 加密你的笔记。 

`纯文本的长期性:` 与 Roam Research 等 web 解决方案不同,笔记首先是纯 Org mode 文件—— Org-roam 仅构建一个辅助数据库,以赋予个人知识库超能力。拥有纯文本笔记对知识库的长期性至关重要。无需担心专有 Web 解决方案被取消。即使 Org-roam 不复存在,笔记仍然有效。 

`免费和开源:` Org-roam 既免费又开源,这意味着如果你对 Org-roam 的任何部分感到不满意,你可以选择扩展 Org-roam 或打开 pull 请求。 

`利用Org mode生态系统:` 几十年来,Emacs 和 Org mode 已发展成为组织纯文本的成熟系统。在 Org mode 的基础上构建 Org-roam 已将其远远领先于许多其他解决方案。 

`基于Emacs:` Emacs 也是编辑文本的绝佳界面, Org-roam 继承了 Emacs 可提供的强大文本导航和编辑功能。 


## 目标受众 {#目标受众}

Org-roam 对不熟悉 Emacs 和 Org mode 的任何人来说看起来都不友好,但对愿意投入精力掌握其复杂性的人来说,它也是极其强大的。 Org-roam 站在巨人的肩膀上。Emacs 最初于1976年创建,至今仍然是许多人编辑文本和设计文本界面时的首选工具。Emacs 的可塑性允许创建 Org mode,这是一种通用的纯文本系统,用于维护待办事项列表、规划项目和撰写文档。这两种工具都非常广泛,需要花费大量时间才能充分掌握。 

Org-roam 假定仅对这些工具有基本熟悉度。使用基本文本编辑功能轻松上手并不困难,但只有在使用这些工具变得更加高级时,人们才能充分意识到将 Roam 功能构建到 Emacs 和 Org mode 中的强大功能。 

Org-roam 的一个关键优点是基于 Emacs,这使得它具有可塑性。这对于记笔记工作流程尤其重要。我们认为记笔记工作流程极其个人化, 没有一种工具能完美适合您。Org mode 和 Org-roam 允许您发现什么适合您,并为自己构建那个完美的工具。 

如果您是新手软件,选择相信这一飞跃,我希望您能像 Neal Stephenson 一样深深着迷。 

> 与其他编辑软件相比,Emacs 的优点就像正午的阳光比星星更大更亮一样,它不仅更大更亮,简直令其他所有东西消失无踪。——尼尔·斯蒂芬森,《一开始就是命令行》(1998年) 


## 简单介绍 zettelkasten 法 {#简单介绍-zettelkasten-法}

Org-roam 提供维护 slip-box 数字滑盒的实用程序。本节旨在简要介绍 `slip-box` 或 `Zettelkasten` 方法。通过提供有关该方法的背景知识,我们希望 Org-roam 的设计决定变得清晰,这将有助于适当使用 Org-roam。在本节中,我们将介绍 Zettelkasten 社区和 Org-roam 论坛中常用的术语。 

Zettelkasten 是一种个人思考和写作工具。它非常重视观点之间的联系,建立起思维网络。因此,它非常适合知识工作者和智力任务,如进行研究。Zettelkasten 可以充当研究伙伴,与之交谈可能会产生新的和意想不到的思路。 

这种方法归功于德国社会学家尼克拉斯•卢曼,他使用这种方法产生了大量的著作。卢曼的滑盒简单地是一盒卡片。这些卡片很小——通常只够容纳一个概念。大小限制鼓励将想法分解成单独的概念。这些想法是显式链接在一起的。想法的分解鼓励对想法的离题探索,增加了思考的表面。在笔记之间明确建立链接也鼓励一个人考虑概念之间的联系。 

在每张卡片的角落,卢曼给每张卡片附加了一个有序ID,允许他链接和跳转到卡片之间。在 Org-roam 中,我们简单地使用超链接。 

Org-roam 是数字化在 Org mode 中的滑盒。每个 zettel(卡片)都是纯文本 Org mode 文件。与维护纸质滑盒一样,Org-roam 使创建新 zettel 变得易于使用强大的模板系统预填写样板内容。 

`暂时笔记` 

滑盒需要一种快速捕获想法的方法。这些被称为 `暂时笔记` :它们是稍后需要处理或删除的信息或想法的简单提醒。这通常使用 `org-capture` (见 [Org Info: Capture](https://orgmode.org/manual/Capture.html "Emacs Lisp: (info \"(org) Capture\")"))或使用 Org-roam 的每日笔记功能(见 **\*org-roam-dailies** 来实现。这提供了一个收集思想的中心收件箱,以供日后处理成永久笔记。 

`永久笔记` 永久笔记进一步分为两类:文献笔记和概念笔记。文献笔记可以是对某一特定来源(例如书籍、网站或论文)的简要注释,以便日后访问。概念笔记在撰写时需要更多照顾:它们需要自解释和详细。Org-roam 的模板系统支持添加不同模板以促进这些笔记的创建。 

有关 Zettelkasten 方法的更多阅读,Sonke Ahrens 的《如何做到聪明记笔记》是一本不错的指南。 

