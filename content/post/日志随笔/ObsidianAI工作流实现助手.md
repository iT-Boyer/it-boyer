+++
title = "QuickAdd 插件实现 ObsidianAI 工作流助手"
description = "博客简介"
date = 2023-06-05T00:05:00+08:00
draft = false
authors = ["iTBoyer"]
password = ""
+++

如果你能利用 Obsidian 中现有的所有工作流程,通过几个简单步骤就添加人工智能的功能,那会是什么样的体验? 

想象一下: 

-   创建一个新笔记,人工智能会根据你已经建立的链接推荐写作提建议,这样你就不会再面临空白页面。
-   在写笔记时,人工智能可以作为知识搏斗的伙伴。
-   通过简单的英语指示轻松改变你写作的结构。

以上这些,以及更多功能,现在都可以在 Obsidian 中实现。 

用于 QuickAdd 的新人工智能助手是一个强大的工具,它集成了 OpenAI 的 GPT-3 和GPT-4模型,在你的思维环境中提供功能强大的人工智能助手。它可以自动化琐碎的任务,节省时间专注于重要的事情,并通过作为知识对手来增强你的思考。 

你只需要 QuickAdd 插件和一个[OpenAI API 密钥](https://platform.openai.com)。这里有[TfTHacker 的精彩指南](https://tfthacker.medium.com/how-to-get-your-own-api-key-for-using-openai-chatgpt-in-obsidian-41b7dd71f8d3)来获取这样的 API 密钥。 

这里有一个 [Twitter 主题](https://twitter.com/chrisbbh/status/1647670976035815424) 展示了你可以用人工智能助手做什么。我将在本指南中展示所有这些示例。 


## [从0到AI驱动](https://bagerbach.com/blog/obsidian-ai#from-0-to-ai-powered) {#from-0-to-ai-powered}

1.  在 Obsidian 中,创建一个AI提示模板文件夹。记住这个文件夹的路径,以后会需要。例如 `bins/ai_prompts` 。
2.  打开 QuickAdd 设置。打开 “AI Assistant” 设置,将其路径设置为AI提示模板文件夹。
3.  在同一选项卡设置中,将 OpenAI API 密钥粘贴到"OpenAI API 密钥"字段中。

这就是所有的设置了。现在你可以使用AI助手了。 

基本思想是设置一个 QuickAdd 宏,它将触发AI助手。AI助手将使用你指定的提示模板生成一个提示,然后将其发送到 OpenAI。OpenAI 将返回一个响应,AI助手将该响应传递给 QuickAdd 宏。然后,您可以在宏的后续步骤中使用该响应,例如捕获到笔记或创建新笔记。 

**创建提示模板很简单:只需在提示模板文件夹中创建一个笔记。** 

你可以使用 [QuickAdd格式语法](https://quickadd.obsidian.guide/docs/FormatSyntax)或[QuickAdd内嵌脚本](https://quickadd.obsidian.guide/docs/InlineScripts)的任何语法。您将在[我的工作流部分](https://bagerbach.com/blog/obsidian-ai#my-ai-powered-workflows)中看到一些此功能的强大示例。 

这里有一个非常简单的示例设置,仅仅通过几个步骤就从0到AI驱动。 

我有一个AI提示模板文件夹,如推荐。我的基本AI助手宏设置只使用AI助手和捕获,如上图GIF所示。 

插入当前文档唯一的区别是我的捕获使用以下捕获格式: 

```text
{{selected}}

{{value:output}}
```

`{{selected}}` 可以为空或选择的任何文本,这使得它非常适合在活动文件中使用AI。 `{{value:output}}` 是助手的输出。 

但是AI助手可以被用作更复杂宏中的构建块,正如我们将看到的。 


## [The AI Assistant Settings](https://bagerbach.com/blog/obsidian-ai#the-ai-assistant-settings) {#the-ai-assistant-settings}

在 AI 助手的主要设置中,可以通过 QuickAdd 设置访问,您可以设置以下选项: 

-   OpenAI 的API密钥
-   提示模板文件夹,包含您的所有提示模板
-   默认模型,确定 OpenAI 将使用的模型
-   是否显示助手,用于切换状态消息
-   默认系统提示模板,将确定模型的行为

<style>.foo img { border:2px solid black; }</style>

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_Assistant_Settings.png&w=3840&q=75" alt="Org mode logo" class="foo" width="300" >}} 

然后,对于宏中的每个AI助手命令,您可以设置以下选项: 

-   提示模板,确定要使用的提示模板。如果不指定,AI助手将要求您从提示模板文件夹中选择一个。
-   模型,确定 OpenAI 将使用的模型。这将覆盖主设置中设置的默认模型。
-   输出名称变量,确定将包含AI助手输出的变量的名称。如果要在宏的后续步骤中使用输出,这很有用。默认情况下,这是 `output`,即 `{{value:output}}` 。还将有一个 `{{value:output-quoted}}` 变量,其中将包含输出,但使用 markdown 块引用格式。例如,如果要在提示中使用输出,这很有用。
-   系统提示模板,确定模型的行为。这将覆盖主设置中设置的默认系统提示模板。

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_Assistant_Command_Settings.png&w=3840&q=75" >}} 


## [My AI-Powered Workflows](https://bagerbach.com/blog/obsidian-ai#my-ai-powered-workflows) {#my-ai-powered-workflows}

下面是我在工作中发现的一些有用的工作流。 

我将整个提示模板作为一个整体包括在内,以便您可以将其复制并粘贴到自己的AI提示模板文件夹中。这些工作流中的一些涉及一些脚本编程,但我试图注释代码,以便易于理解您正在运行的内容。 


### [Generating writing prompts](https://bagerbach.com/blog/obsidian-ai#generating-writing-prompts) {#generating-writing-prompts}

输入链接,AI 根据与该笔记相关的笔记生成10个写作提示。 

这个提示模板使用 Dataview (必需的插件)来获取与您输入的笔记相关的所有笔记。 

````md
这里是与{{value:Link}}相关的 Obsidian(Markdown)链接列表。
请使用这些链接以项目符号形式给出关于{{value:Link}}的10个写作提示。
在段落中将链接作为句子的一部分使用。

```js quickadd
//使用DataView API的简称
const dv = DataviewAPI;
//提示用户输入MoC笔记的链接
const targetLink = await this.quickAddApi.inputPrompt(
    'MoC链接',
    "输入我们将使用的链接以获取所有与其链接的笔记。"
);
this.variables['Link'] = targetLink;

//获取与目标链接链接的所有笔记的列表
const healthPages = dv.pages(targetLink).values;
//将笔记格式化为[[links]]
const links = healthPages.map((f) => {
    const tf = app.vault.getAbstractFileByPath(f.file.path);
    return app.fileManager.generateMarkdownLink(tf, '/');
});

//将列表连接起来,使其成为文本格式,每个链接占一行
return links.join('\n');
```
````


### [MOC Creator](https://bagerbach.com/blog/obsidian-ai#moc-creator) {#moc-creator}

这是我在 [Twitter工作流](https://twitter.com/chrisbbh/status/1647670981417156609) 中展示的MOC大纲创建者。生成文档导航和内容这是一个改进版本,与上面的提示一样,它会要求您输入一个链接,然后为该笔记生成一个MOC。 

````md
这里是与{{value:Link}}相关的Obsidian(Markdown)链接列表。
请使用这些链接为关于{{value:Link}}的笔记写出大纲。最多不超过200字。

在段落中将markdown链接作为句子的一部分使用。

```js quickadd
//使用DataView API的简称
const dv = DataviewAPI;
//提示用户输入MoC笔记的链接
const targetLink = await this.quickAddApi.inputPrompt(
    'MoC链接',
    "输入我们将使用的链接以获取所有与其链接的笔记。"
);
this.variables['Link'] = targetLink;

//获取与目标链接链接的所有笔记的列表
const healthPages = dv.pages(targetLink).values;
//将笔记格式化为[[links]]
const links = healthPages.map((f) => {
    const tf = app.vault.getAbstractFileByPath(f.file.path);
    return app.fileManager.generateMarkdownLink(tf, '/');
});

//将列表连接起来,使其成为文本格式,每个链接占一行
return links.join('\n');
```
````


#### [MOC Creator with content](https://bagerbach.com/blog/obsidian-ai#moc-creator-with-content) {#moc-creator-with-content}

这是MOC创建者的延伸,它还包括您要为其创建MOC的笔记的内容。文档导航和内容 

````md
这里是与{{value:Link}}相关的Obsidian(Markdown)链接列表,以及它们的内容。
请使用这些链接为关于{{value:Link}}的笔记写出大纲，最不超过多200个词。

在段落中将markdown链接作为句子的一部分使用。

```js quickadd
//使用DataView API的简称
const dv = DataviewAPI;
//提示用户输入MoC笔记的链接
const targetLink = await this.quickAddApi.inputPrompt(
    "MoC链接",
    "输入我们将使用的链接以获取所有与其链接的笔记。"
);
this.variables["Link"] = targetLink;

//获取与目标链接链接的所有笔记的列表
const healthPages = dv.pages(targetLink).values;
//将笔记格式化为[[links]]
const links = await Promise.all(healthPages.map(async f => {
    const tf = app.vault.getAbstractFileByPath(f.file.path);
    const fileContent = await app.vault.cachedRead(tf);
    const link = app.fileManager.generateMarkdownLink(tf, '/');

    return `## ${link}\n${fileContent}`
}));

//将列表连接起来,使其成为文本格式,每个链接占一行
return links.join("\n");
```
````


### [Summarizer](https://bagerbach.com/blog/obsidian-ai#summarizer) {#summarizer}

这是一个简单的提示,您选择一些文本,然后使用该提示调用助手。然后,它将吐出一个AI生成的摘要: 

````md
请摘要如下文本。仅使用文本本身作为摘要材料,不要添加任何新内容。为简洁起见,以大纲形式重写此内容:
{{value}}
````


### [Transform selected](https://bagerbach.com/blog/obsidian-ai#transform-selected) {#transform-selected}

这个提示模板将要求您提供说明,它将使用这些说明来转换您选择的文本。 

它基本上可以做任何事情 - 不仅仅是转换。 

您可以通过提供与摘要提示中类似的说明来要求它概述文本,解释您选择的文本,以大纲形式重写某些内容,或任何您想要的内容。 

我要求它通过使用符号作为参考来解释我选择的 LaTeX 方程式,它完美地解释了每个符号以及整个公式。 

````text
说明:
{{VALUE:Instructions}}

请按上述说明转换文本:
{{selected}}
````


### [Flashcard creator](https://bagerbach.com/blog/obsidian-ai#flashcard-creator) {#flashcard-creator}

这个提示模板根据选择的文本生成背诵卡。 

````text
请以以下形式创建记忆卡:
Q: 问题在这里
A: 答案在这里

问题应该简洁、简明、简单,不应该是一个双重问题。避免一般性问题和双重否定问题。
答案也应该简短简单。

如果无法将材料包含在一个问题中,请创建多个记忆卡。

---

{{value}}
````


### [Get me started writing about...](https://bagerbach.com/blog/obsidian-ai#get-me-started-writing-about) {#get-me-started-writing-about}

为了启动您的写作: 

````text
我正在写关于{{value:Topic}}的文章。

请给我一些要点来开始:

- 相关主题是什么?
- 主题的基础知识是什么?
- 为什么创建"{{value:Topic}}"主题?
- 它解决了什么问题?
````


### [Manual prompt](https://bagerbach.com/blog/obsidian-ai#manual-prompt) {#manual-prompt}

我认为这几乎是必不可少的。它类似于简单地打开 ChatGPT 并提示它。 

该模板将要求您提供一些输入,这是整个提示。 

````text
{{value}}
````

这也是我在简单设置示例中演示的一个模板。 

它允许您输入任何 Prompt,将其发送到AI,并将其响应捕获到宏中以进行进一步使用。非常强大,几乎可以实现任何功能。 


### [Alternative viewpoints](https://bagerbach.com/blog/obsidian-ai#alternative-viewpoints) {#alternative-viewpoints}

当我陷入困境时,这个模板对我很有帮助。从另一个角度看问题总是很好的。您需要选择一些文本(您的草稿),然后在提示时输入一个主题。 

````text
我正在写关于{{value:Topic}}的文章。这里是我的草稿:

{{value}}

请提出3个改进建议,3个不同观点和3个用于进一步思考的问题。
````

它会分析您的草稿并提供: 

-   3个改进建议 - 可以改进的方面
-   3个不同观点 - 另一种看问题的方式
-   3个问题进行进一步思考 - 推动您在写作中更深入思考的问题

这真的有助于打破思维定势,找到新的灵感来源,并激发创造性思维。人工智能可以以全新的方式审视问题,这真的很有价值。 

我经常在写作时使用这个模板。它不仅可以解决特定的写作难题,还可以在更广泛的层面上扩展我的思维。 


### [Prompt Chaining](https://bagerbach.com/blog/obsidian-ai#prompt-chaining) {#prompt-chaining}

好的,让我们来看一些更高级的东西。 

提示链是指一个提示使用前一个提示的输出。从本质上讲,您正在使AI逐步思考——就像遵循配方。 

[这里有一个疯狂的例子可以生成初创企业名称→商业计划、目标用户等→为您提供一步一步的实施指南](https://twitter.com/chrisbbh/status/1647670986496520193)。 

正如我前面提到的,AI助手可以被视为一个建筑块。您可以把它们堆叠在一起以获得强大的结果。 

这里有三个提示的堆栈,每个提示都使用前一个提示的结果。 

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_Assistant_Chainprompt_1.png&w=3840&q=75" >}} 

当您配置每个提示以具有特定设置时,您可以完全自动化它: 

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_Assistant_Chainprompt.png&w=3840&q=75" >}} 

例如,这是第一步。它使用 `ChainPrompt1` 模板并在 `{{value:out1}}` 中输出其值。而且由于这是一个宏,每个输出变量可以在后续步骤中使用。 

通过堆叠助手许多次并使用每个步骤消耗前一个步骤的输出变量的模板,我们得到了提示链。 

最终捕获的格式如下: 

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_assistant_Chainprompting_2.jpeg&w=3840&q=75" >}} 

````text
## Iteration 1

{{value:out1}}

## Iteration 2

{{value:out2}}

## Output

{{value:out_final}}
````


### [Summarize book notes](https://bagerbach.com/blog/obsidian-ai#summarize-book-notes) {#summarize-book-notes}

这是我使用AI助手构建的第一个工作流之一。 

这里是我以前将书籍笔记导入 Obsidian 的工作流程: 

1.  从 Readwise 获取高亮部分
2.  在笔记本中创建笔记

新的AI驱动的工作流程: 

1.  从 Readwise 获取高亮部分
2.  AI助手根据高亮部分概括书籍
3.  在笔记本中创建带有摘要的笔记

核心工作流程实际上是相同的。我只需要选择一本书,就可以导入,所以我的输入没有改变。 

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_Assistant_Book_Summary.jpeg&w=3840&q=75" >}} 

您也可以获得这个工作流程!这里是我们需要设置的宏: 

{{< figure src="https://bagerbach.com/_next/image?url=%2Fuploads%2FAI_Assistant_Book_Summary_Macro.png&w=3840&q=75" >}} 

第一个和最后一个步骤都来自我的博客文章[我如何将文献笔记导入Obsidian](https://bagerbach.com/blog/importing-source-notes-to-obsidian)。如果您以前已经遵循过,您需要再次这样做,因为我最近更新了脚本和说明,以实现更强大的工作流程(在制作AI助手之前)。 

中间步骤是AI助手。您需要将其设置为使用GPT-4模型以获得更大的上下文大小。我发现GPT-3.5对于这个任务来说太局限了。然后,您需要配置AI助手使用特定的模板,我称之为 `概括书籍笔记` 。 

这里是模板: 

````text
请概括如下书籍"{{value:title}}"作者{{value:author}}。仅使用文本本身作为摘要材料,不要添加任何新内容。为简洁起见重写此内容:

{{value:highlights}}
````

这些值中的每一个都来自 EzImport 脚本,这就是为什么宏的排序必须是 EzImport → AI助手 → 捕获。 

所以,请先按照[指南](https://bagerbach.com/blog/importing-source-notes-to-obsidian)进行操作。在工作流程的中间注入AI助手,设置如上所示...就是这样。 

除了摘要之外,您可以在宏中添加另一个步骤来创建可操作的要点。使用下面的模板进行此操作。 

最后,这里是我用于所有书籍笔记的书籍笔记模板。非AI版本在上述指南中找到: 

````md
---
image:
tags: in/books state/process
aliases:
  - "{{VALUE:title}}"
cssclass:
lastHighlightAt: {{VALUE:lastHighlightAt}}
shouldPublish: true
---

# {{VALUE:title}}

## 元数据
标签:: {{VALUE:tags}}
类型:: [[{]]
作者:: {{VALUE:author}}
参考::
评级:: {{VALUE:10,9,8,7,6,5,4,3,2,1,0}}
审阅日期:: ==更新此处==
完成年份:: [[{{DATE:gggg}}]]

# 想法

# 采取的行动/变化
> [!todo] 可操作要点 - AI
{{value:actionable-takeaways-quoted}}

# 主要要点摘要
> [!tip] AI 摘要
{{value:summary-quoted}}

# 高亮和笔记
````


### [Actionable takeaways](https://bagerbach.com/blog/obsidian-ai#actionable-takeaways) {#actionable-takeaways}

根据您提供的文本创建可操作的要点。这需要一个 `summary` 变量,您可以从宏中的前一步(例如使用“概括书籍笔记”工作流程)获得。 

````text
根据以下文本编写可操作的要点。编写一步一步的计划。首先给我一些我可以立即采取行动的东西,然后告诉我根据文本我还可以做什么。使用80/20原则优先考虑最重要的要点。

{{value:summary}}
````


### [YouTube Summarizer](https://bagerbach.com/blog/obsidian-ai#youtube-summarizer) {#youtube-summarizer}

这个提示模板将允许您获取 YouTube 视频的字幕,然后对其进行摘要。 

您需要提供的只是希望创建新笔记时的 YouTube 视频 URL。 

我将此与[Actionable takeaways](https://bagerbach.com/blog/obsidian-ai#actionable-takeaways)提示模板结合使用,以创建具有摘要和可操作要点的笔记。 

因此,我的宏看起来像这样: 

1.  YouTube 摘要 - AI助手步骤获取字幕并进行摘要。它还为下一步设置了几个变量。
2.  可操作要点 - AI助手步骤根据摘要创建可操作要点。
3.  创建笔记 - 创建具有视频标题、摘要和可操作要点的新笔记。

第三步- 创建新笔记 - 是一个模板操作,它创建一个文件名为 `+ {{VALUE:safeTitle}}` 的新笔记。 `safeTitle` 来自宏的第一步,表示视频的标题,但移除了所有非法字符。 

对YT摘要的GPT-4限制似乎是~20分钟的视频长度。 

````md
请对以下文本进行摘要。仅使用文本本身作为摘要材料,不要添加任何新内容。为简洁起见,以提纲形式重写:
```js quickadd
const videoUrl = await this.quickAddApi.inputPrompt("Video URL");

const url = new URL(videoUrl);
const videoId = url.searchParams.get('v');

this.variables["📺 YouTube Video ID"] = videoId;

const transcriptBody = await requestUrl(`https://youtubetranscript.com/?server_vid=${videoId}`).text;
const videoPage = await requestUrl(videoUrl).text;

console.log(transcriptBody);
console.log(videoPage);

const dp = new DOMParser();
const dom = dp.parseFromString(transcriptBody, 'text/xml');
const transcriptEl = dom.getElementsByTagName('transcript')[0];
if (!transcriptEl) {
    new Notice("No transcript found.");
    throw new Error("No transcript found.")
}
const textElements = transcriptEl.getElementsByTagName('text');

const textArr = [];
for (let i = 0; i < textElements.length; i++) {
  const text = textElements[i];
  textArr.push(text.textContent);
}

const transcript = textArr.join("\n");

const title = videoPage.split('')[1].split('')[0].replace(" - YouTube", '').trim();

function sanitizeFileName(fileName) {
  const illegalChars = /[\/\?<>\\:\*\|"]/g;
  const undesiredChars = /"/g;
  const replacementChar = '_';
  return fileName.replace(illegalChars, replacementChar).replace(undesiredChars, "");
}

function sanetizeTitle(text) {
  const undesiredChars = /"/g;
  const replacementChar = "";
  return text.replace(undesiredChars, replacementChar);
}

this.variables["title"] = sanetizeTitle(title);
this.variables["safeTitle"] = sanitizeFileName(title);

return transcript;
```
````

这里是我的 YouTube 视频笔记模板: 

````md
---
tags: in/video state/process
aliases:
  - "+ {{VALUE:title}}"
cssclass: null
image: https://i.ytimg.com/vi/{{VALUE:📺 YouTube Video ID}}/hqdefault.jpg
---

Title:: {{VALUE:title}}
Type:: [[+]]
Tags::
URL:: https://www.youtube.com/watch?v={{VALUE:📺 YouTube Video ID}}
Channel::
Rating:: {{VALUE:10,9,8,7,6,5,4,3,2,1,0}}
Reference::
Publish Date::
Reviewed Date:: ==UPDATE THIS==

---

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/{{VALUE:📺 YouTube Video ID}}" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center>

---
> [!info] AI Summary
{{value:summary-quoted}}

> [!todo] Actionable Takeaways
{{value:actionable-takeaways-quoted}}

<% tp.file.cursor(0) %>
````


## [Final words](https://bagerbach.com/blog/obsidian-ai#final-words) {#final-words}

这还处于初期阶段。我有那么多的工作流程想法,提示和其他我们可以与AI助手一起做的酷事情。 

如果您有任何问题,请让我知道。所有我的提示模板也可以在AI助手的同一页面上找到。 

我很想知道您如何在保险库中使用AI。欢迎在 Twitter 上@我。 

请记住,使用API会产生货币成本。GPT-3.5相当便宜,但过度使用GPT-4可能会造成高昂的成本。出于这个原因,我强烈建议您在 OpenAI 账户上设置支出限额。尝试玩转模型以了解适合您的模型。 

这个工具——以及AI一般来说——是一个重大的生产力乘数。它绝不应取代您的思考,只应增强它。让它处理PKM的琐碎部分,这样您可以专注于最重要的事情。 

