+++
title = "auto-gpt：将思维框架赋予chatgpt"
description = "博客简介"
date = 2023-04-19T17:03:00+08:00
lastmod = 2023-04-19T18:44:23+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

auto-gpt 是一款提前设定目标，然后交给 chatgpt 自问自答的一些的操作，人类仅做好每一步的选择就好，大大简化了对话过程。 

相比普通的聊天模式，缺乏和用户的交互的灵活性。 

auto-gpt 对于在工作生活中积累一些工作流程模板和习惯的人比较友好， 现在只需要把目标写入 yaml 模板文件，然后通过 --ai-setting 启动 auto-gpt 。就能解放双手，作选择就行了。 auto-gpt 在每完成一步计划，会询问是否执行，也可以将个人看法反馈给 auto-gpt 。 

提醒自己在日常工作生活中，要养成 `以终为始` 的习惯，有目的的做事，才能遇强则强。 

想有快速掌握各种思维模式，可以参考 clickprompt 项目，上面收集了针对不同业务的行为规范模板。分析自己的需求，找到相应的模板，例如：对话，写作，开发等，都有相应的模板。借助模板能够提升看待问题解决问题的方式。auto-gpt 就是通过提供 yaml 模板，大大提升了 chatgpt 沟通效率。 

auto-gpt 自带一套模式：角色，职责和 5 个目标。auto-gpt 在接收到给定的模板后，会自动分析需求，优化目标，拆分任务，产出高质量的行动方案。在 clickprompt 上提供了更多好用的业务模板，经过简单处理成 yaml 模板，就可以激发 auto-gpt 更多的业务能力，善用这些模板，就能在不同领域和角色上，激发出惊人的潜力，例如：写作，开发，演讲。 

现在有很多现成的 prompt 项目，提供针对不同需求的角色的提示语，配合 clickprmpt 能让 chatgpt 更专业，更加精准的回答你的问题。 


## 辅助工具推荐 {#辅助工具推荐}

在面对众多 prompt 提示语的轰炸，如何高效管理这些碎片话的语句，就迫在眉睫。 

建议找一款适合自己的 snippet 工具，把常用的 prompt 整理成快捷输入的命令，帮助记忆和输入。 

在 mac 用户，推荐 alfred snippet 的功能，优化网上有很多介绍。 

alfred 推出了 chatfred 最为 chatgpt 前端，彻底把 chatgpt 融入到了生活的方方面面。 

作为 emacs 用户用脚本生成了 snippet 快，结合 org-ai 让写作，知识管理更加智能有趣。 

之前积累了很多提上来个写作，项目管理，知识管理，很多提升效率的模板，类似 clickprompt 工作流模板。 

准备在现有的模板上，添加 chatgpt prompt 扩展，让 AI 随叫随到。 

举例：输入 `ai` 自动展开和 AI snippet 对话。 

```org { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
#+begin_ai
[SYS]: You are a helpful assistant.

[ME]:
#+end_ai
```


### <span class="org-todo todo TODO">TODO</span> 待完善的模板 {#待完善的模板}

1.  ox 博客模板：在写作的过程中，就可以借助模板的方式，编写博客
2.  tj3 项目管理模板： 创建项目任务计划等

