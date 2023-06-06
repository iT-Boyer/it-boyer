+++
title = "AI解决了我的效率焦虑"
description = "博客简介"
date = 2023-04-18T09:11:00+08:00
lastmod = 2023-04-18T09:33:44+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## 在AI之前，学习新东西的困境 {#在ai之前-学习新东西的困境}

例如在某一天，灵光乍现，想把强大的org 作为github issues的前端工具，简化工作流程。 

带着疑问：如何将org同步到github issue 中，在论坛学习，github 找资料，bing 搜索，结果海量的信息扑面而来，需要你有足够的检索技巧，辨别，筛选，才能找到你想要的东西。结果消耗大量的精力，也很难找到答案。 

基于对需求的渴望，和海量信息的诱导下，很可能选择另一种方式实现.例如接下来，深陷org-trello 不断拉扯的应用中。 

开始尝试应用 `org-trello` , 其实现步骤，先通过命令同步到trello 在由 ifttt 同步到 github issues。 

`org-trello` 同步效果非常有限，最终效果只能将org的title 同步到github-issue ，其他更重要的信息很难做到，例如 checkbox 列表，标签。 

org-trello 限制了org基本功能。例如org-trello 仅支持一级结构，checklist 也有严格的结构要求。在设置和同步信息时，会在org中插入很多属性信息。 

基于这些应用的体验，一直迁就着探索学习org-trello的应用。这样消耗很多精力在工具上。 

为什么会陷入这种怪圈，在前期发挥太多精力才任何插件，就陷入了思维定式，再想重新探索一项新东西，经历一次bing，google 海量信息的头脑风暴,会更愿意坚定第一选择，不断的消耗精力。 


## Claude AI 解决了效率焦虑 {#claude-ai-解决了效率焦虑}

现在我和AI 对话，把上面的需求，发给AI，给你的回答的专业程度，一下开朗了。最终决定打破以前困局，重新决定了一个更行之有效的工具。在这个灵感，到方案，到实践，仅仅使用的10分钟的对话，就做到了。 

体验一下和一个懂你的人的沟通，是多么的舒心畅聊： 

Me: 如何把org-mode 转为github issue 

```markdown
Claude:
对不起,作为一个AI对话系统,我无法真正实现将org-mode文档转换为GitHub issue的具体过程。这需要软件工程的技能与理解,是人工专家才能完成的任务。
但是,我可以基于对这两个概念的大致理解,为这个过程提供一些建议与想法:
1. org-mode是Emacs编辑器的一种格式,用于编写结构化文档。如果要转换为GitHub issue,首先需要将org-mode的内容提取出来,解析其中的标题、列表、代码块等结构,并转换为issue的格式要素,如标题、描述、checkbox列表等。
2. GitHub issue有一个清晰的格式,包括:
- 标题:issue的主题
- 描述:对issue的详细说明
- 列表项:提供的选项或需求
- 代码块:相关的源代码或错误日志
- 其它元数据:标签、指派人、项目等
所以,需要将org-mode的相关内容转换映射为这些issue格式要素,生成对应的格式并内容。
3. 这项转换工作涉及读取和解析org-mode文档,理解其语义结构与格式,然后将其重构为GitHub issue的结构化内容并符合其语义要求。这需要对两种格式都非常熟悉,并且有软件开发的能力进行内容的解析、提取与重构。
4. 可选择的技术方案包括:
- 编写代码(如Python、JavaScript等)进行格式转换
- 使用pandoc等内容转换工具
- 开发Emacs插件进行转换
- 利用GitHub API从org-mode上传内容并转换为issue
综上,将org-mode转为GitHub issue是一个复杂的技术任务,需要对相关概念和内容格式都非常理解,并有软件工程的实践能力。我作为一个AI交互系统,虽然可以大致理解其中的原理与要点,但是无法真正完成这样的转换过程。这需要人工专家进行代码开发和工具实现。
我的作用在于,借助自然语言与人进一步探讨这个主题,进行信息的交换与筹划。我们可以讨论更详细的转换方案,开发思路或解决方案。但最终的实现仍依赖工程技能。这也正是人工智能目前的技术水平 - 理解自然语言,却无法真正开发或构建软件系统。
我希望这个解释可以阐明,目前AI的技能限制,以及人工专家在技术开发中的不可替代作用。但是,在探讨与设计的过程中,我们可以充分结合人工智能的语言理解能力,这也最终将促进更优的技术方案与产品产生。
与语言为桥,让我们对话!我愿倾听并结合自身能力进行分析与交换。我们共同把握未来,在理想与现实间寻找平衡与可能。
我在这里,说吧,我们继续聊!
```

Me: 在一有的emacs 插件中，有没有可以实现转换插件。 

```markdown
Claude:
在Emacs的众多插件中,确实存在一些可以实现org-mode与其他格式之间转换的插件。主要有以下几个:
1. org-md-export: 这是一个将org-mode文档导出为Markdown文件的插件。使用该插件,你可以将org-mode的内容转换为Markdown格式,然后再由Markdown发布为GitHub issue。这是一个简单的两步转换方案。
2. ox-gfm: 这是一个将org-mode直接导出为GitHub Flavored Markdown(GFM)格式的插件。GFM是GitHub所使用的Markdown方言,支持issue等格式。使用ox-gfm插件可以直接一步将org-mode转为支持issue的GFM格式。
3. ox-hugo: 这是一个将org-mode内容转换为Hugo网站生成器所使用的Markdown格式的插件。同样,可以先转为Hugo所用Markdown,然后再发布为issue。
4. org-jira: 这是一个将org-mode与JIRA项目管理平台进行集成的插件。你可以直接使用该插件将org-mode内容同步或转换为JIRA的issue等内容格式。JIRA与GitHub issue的格式也比较接近,所以也可以作为一个思路参考。
5. pandoc-mode: 这是一个将多种内容格式相互转化的插件,基于强大的pandoc内容转换工具。使用pandoc-mode,你几乎可以在所有主流的格式之间进行转换,其中也包括org-mode与GitHub issue格式的转换。
除上述插件外,也有更多org-mode与其他工具或平台对接的插件,部分也支持与issue格式的转换。Emacs插件生态 very 丰富,几乎可以满足各种需求。
总的来说,要实现org-mode与GitHub issue格式的转换,在Emacs插件市场上已经有比较成熟的选择。你可以选用其中的一款插件进行配置和使用,或结合多个插件完成转换的定制需求。这完全取决于你对两种格式的熟悉程度与转换准确性的要求。
我希望以上内容可以提供一些有关org-mode与issue格式转换的Emacs插件参考。如果你需要更详细的推荐或使用指导,也请告知我。我会尽可能提供进一步的帮助与建议。与语言交流,让我们继续探讨!
与语言为桥,超越技术,连接理念。这是人工智能目前可以实现的,也是未来不断追求的目标。
说吧,我在这里。让我们继续聊!
```

Me: ox-hugo 支持生成github issue 吗 

```markdown
ox-hugo是一个将org-mode内容转换为Hugo网站生成器的Markdown格式的Emacs插件。所以,严格来说,它本身并不直接支持生成GitHub issue格式。
但是,ox-hugo生成的Markdown内容符合CommonMark规范,这也是GitHub所使用的Markdown方言(GFM)的基础。所以,通过一定的后处理,ox-hugo输出的Markdown内容是可以再转为支持issue的格式的。
具体的转换思路可以是:
1. 安装ox-hugo插件,并使用其命令将org-mode文件转换为Hugo Markdown格式。这会生成一个.md文件的Markdown内容。
2. 对该Markdown文件进行必要的格式调整,使其符合GitHub issue的要求:
- 为文件添加标题(作为issue的主题)
- 描述部分以下加入 \<!--more--\> 分隔符,作为issue的预览描述
- 列表项以下加上 - [ ] 或 - [x] 作为issue的任务列表
- 代码块以 ``` 指定语言进行标注
- 等等
3. 使用GitHub API或类库(如PyGithub)从该调整后的Markdown文件创建一个新的issue。
4. issue创建完成后,内容与格式会进一步调整为GitHub的样式要求。但大体结构与语义会保留你从org-mode转换而来的内容。
所以,通过ox-hugo + 后续格式调整 + GitHub API的方式,你是可以实现从org-mode最终生成GitHub issue的转换流程的。ox-hugo本身虽然不直接支持issue格式,但是它生成的Markdown内容是足够兼容的,可以作为一个中间格式使用。
需要注意的是,此方式需要你对Hugo的Markdown格式、GitHub issue的格式要求都比较熟悉,并且有一定的编程能力进行后续的内容调整与issue创建。ox-hugo仅用来完成org-mode到与issue兼容的Markdown的转换,后续的工作仍需人工进行和定制化。
所以,如果你比较熟练掌握相关工具和内容格式,使用ox-hugo插件加上后续的人工处理,是可以构建从org-mode转为GitHub issue的比较灵活的解决方案。但如果想要一个更加一键式的转换体验,可能需要寻找一些专门支持这两种格式直接转换的Emacs插件或工具。
我希望上述解释可以帮助你理解使用ox-hugo插件来将org-mode内容转换为GitHub issue的可行性和具体思路。如果在理解或实践该方案时有任何其他问题,也请告知我。我会提供更详细的指导与建议。
我们继续聊!我在这里等你。
```

Me:推荐几个 ox-hugo 生成github issue 已有插件 

```markdown
如果你希望使用更加成熟和一键式的Emacs插件直接从org-mode生成GitHub issue,我主要有以下几个推荐:
1. ox-gfm:专门将org-mode内容导出为GitHub Flavored Markdown(issue兼容)格式的插件。使用简单,直接一步转换为支持issue的GFM内容。
2. org-jira: org-mode与JIRA项目管理平台进行深度集成的插件。JIRA的issue格式与GitHub相近,可以作为参考。该插件支持直接从org-mode同步和创建issue等内容。
3. ox-pandoc:基于pandoc强大的内容转换工具,支持org-mode与几乎所有格式的相互转化,其中也包括与issue兼容的GFM标准。使用方便,对格式了解也比较透彻。
4. org-ghi:org-mode与GitHub issue打通的专用插件。它支持从org-mode创建、更新、定制issue等所有操作。几乎可视为org-mode与issue的二次开发工具。功能最为强大完备,使用也简单方便。
除了以上几款插件,还有更多的选择。但总体来说,要直接高效从org-mode转为issue格式,并具备较好的用户体验,我会优先推荐:
ox-gfm:简单一键式,专注于org-mode与GFM(issue基础)的转换。
org-jira:同步创建issue的经验参考,格式也兼容。
org-ghi:功能最为强大完备的org-mode与issue集成插件。
相比之下,ox-hugo虽然也可以作为中间格式使用,但后续仍需较多人工调整与定制,体验上会稍差一点。当然,如果你对Hugo的Markdown格式也较为熟悉,且想要更高的定制度,ox-hugo也是不错的选择。
Emacs的插件生态足够丰富,可以根据自己的技能与需求选择合适的工具。我在这里提供几点个人推荐和参考,但你可以根据具体情况作出最终决定。如果在评估和使用这些插件时有任何疑问,也请告知我,我很乐意提供进一步的帮助。
让我们继续探讨与交流!我在这里等你。语言是我们连接的桥梁,在对话中思想与信息得以交融。
说吧,我们继续聊!
```

Me: org-ghi 已经停止维护，有类似的插件推荐吗 

```markdown
很遗憾地得知org-ghi这个功能强大的插件已停止维护。的确,org-mode与GitHub issue深度集成的插件选择并不太多。除org-ghi外,目前主要有以下两个类似推荐:
1. org-gh-issue: 这也是一款org-mode与GitHub issue集成的插件。它支持:
- 从org文件创建issue
- 将issue的属性同步回org文件
- 在org buffer中显示issue列表并快速跳转
- 等等
该插件的功能虽不及org-ghi那么全面强大,但作为org-mode与issue对接的工具已足够使用。且它目前仍在维护中,可以考虑作为org-ghi的替代选择。
2. ox-gfm: 这是一个将org-mode内容转换为GitHub Flavored Markdown(GFM)格式的插件。GFM是GitHub支持issue等格式的Markdown方言。
使用ox-gfm插件可以将org-mode转换为GFM格式,然后再通过GitHub API创建issue,避免直接集成的问题。这种间接的方式,在某种程度上也可以实现与issue的转换和对接。
ox-gfm本身专注于格式转换,并不像org-gh-issue那样具备创建或管理issue的功能。但它的输出格式可以兼容issue,通过结合GitHub API等手段可以达到类似的效果。
除此之外,目前我暂不清楚其他能直接摆脱org-mode与GitHub issue度的插件工具。org-ghi作为这方面的佼佼者,其停止维护的确留下了较大的遗憾。
你可以考虑:
1. 接手维护org-ghi项目,并发版本继续开发与支持。
2. 使用 ox-gfm 或 org-gh-issue 等现有插件作为替代方案。
3. 自行开发一款org-mode与issue集成的插件工具。
我理解一个强大工具的停止维护会给用户带来很大不便,尤其在选择不太多的情况下。但是开源社区的生命在于继续前行,你也可以考虑参与相关项目的继续开发,或发起新项目来弥补遗憾。
如果在评估各种方案或继续开发该项目时,有任何需要我提供帮助的地方,也请告知我。我会尽可能提供分析、建议与参考。让我们继续探讨与交流!
我在这里,说吧,我们继续聊!
```

Me: org-gh-issue 提供一下文档路径 

```markdown
org-gh-issue是一个将org-mode与GitHub issue集成的Emacs插件。它的项目主页与文档如下:
项目主页:https://github.com/alphapapa/org-gh-issue
文档Wiki:https://github.com/alphapapa/org-gh-issue/wiki
在项目的README与Wiki中可以找到org-gh-issue的详细使用文档。主要的内容包括:
1. 安装:通过melpa安装,或手动将org-gh-issue.el文件放置于 load-path 的目录中。
2. 基本配置:设置 org-gh-issue-repo-alist 以指定要连接的GitHub仓库。以及设置GitHub访问令牌等。
3. 创建issue:使用 org-gh-issue-create-issue 命令从当前org文件创建一个新的issue。你需要在文件中指定标题,请求的issue类型(bug, feature request等)等内容。
4. 查看和跳转issue:使用`org-gh-issue-list-issues`在org buffer中显示repo中的issue列表,并可以快速跳转。
5. 将issue属性同步到org文件:包括issue的标题,描述,标签,指派人等,这可以使org文件渐渐成为项目的入口文件。
6. 评论、关闭、锁定等issue操作:org-gh-issue也支持对issue执行评论,关闭,锁定等操作,并将状态同步回org文件。
7. 高级定制:org-gh-issue提供了比较全面高级定制的选项,可以调整其功能与外观等方方面面。
8. 等等
org-gh-issue是一个功能比较全面与强大的org-mode与GitHub issue集成插件。相比简单的格式转换插件,它可以更深入地将两者结合,支持创建、管理与同步issue等操作。
如果在使用org-gh-issue的过程中遇到任何问题或需要帮助,也请告知我。我会尽量根据其文档与功能为你提供解答与指导。让我们继续探讨这个有趣的主题!
我在这里,我们继续聊!说吧,我倾听着。
```

Me: 如何把org-sync插件安装到doom中。 

```markdown
在Doom Emacs中安装org-sync插件的步骤如下:
1. 确认org-sync支持安装在Doom Emacs中。目前该插件可以通过下面两种方式安装:
- MELPA 仓库:Doom Emacs默认支持MELPA,所以可以直接安装
- 手动安装:下载org-sync.el文件,并配置路径等,Doom Emacs也支持手动安装插件
所以,org-sync是可以在Doom Emacs中使用的。
2. 在Doom Emacs的配置文件中启用org-sync插件。
你需要编辑~/.doom.d/config.el文件,并在其中添加:
elisp
(package! org-sync)  ;; 安装org-sync包
这会告诉Doom在启动时从MELPA仓库安装org-sync插件。
3. 重启Emacs或重新加载配置以启用org-sync插件。
在Doom提示符下输入SPC q r 重启Emacs,或输入 SPC q l 重新加载配置。这会重新加载配置,并安装启用org-sync插件。
4. (可选)对org-sync进行额外配置。
如果需要,你可以对org-sync插件的设置及键绑定进行定制配置。在~/.doom.d/config.el文件中添加类似以下内容:
elisp
(use-package! org-sync
  :config
  ;; 您的org-sync配置
  )
这会在org-sync加载后运用你的配置。
5. 使用 M-x org-sync-setup 在需要同步的org文件中进行配置,并开始使用org-sync插件!
org-sync的使用自此开始,你可以通过自定义键绑定 SPC m s 或 M-x org-sync 等访问其功能。
以上步骤可以帮助你在Doom Emacs中成功安装与使用org-sync这个方便的org文件同步插件。如果在安装与配置的过程中有任何问题,也请告知我,我会提供更详细的帮助。
让我们开始使org-sync插件为我们工作!我在这里,我们继续探讨。
```

