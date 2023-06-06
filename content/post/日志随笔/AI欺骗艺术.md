+++
title = "AI 回答的内容会尽量真实，甚至捏造吗"
description = "博客简介"
date = 2023-04-17T12:03:00+08:00
lastmod = 2023-04-17T15:51:49+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

在使用AI 编程的过程中，提出一个问题之后，会尽量作出回答，回答的内容专业程度高，从第一印象上先得到提问者的认可。 

当你惊叹之余，拷贝运行时，官方文档却会给出警告，你使用的方法不存在。 

一度怀疑是自己使用第三库的版本不对，开始检索库版本源码的迭代版本，在issues 中寻找答案，想通过各种方式作证AI 是不会给出错误回答的。 

在作了一系列努力在之后，没有找到任何官方的认可，在心力交瘁，又想到AI 助手，提出疑问时，AI会态度诚恳的抱歉，给出另一个有理有据的答案。 

这时在醒悟到AI 就是一个职场老油条呀。 

AI 的智能取决于使用者，遇强则强。在对ai 对话技巧上，现在网上出现了很多辅助AI 更好的了解你的需求的prompt项目工程。 

在试玩一段时间之后，AI 确实能够胜任很多角色，表现的也相当出色。但是在专业上面，提问者要抱着怀疑的态度，可以通过和AI 对话，激发灵感，具体的实现还是要提升自己的专业能力。 

现在最火的 `auto-gpt` ， `clickprompt` 可以能提升AI 的沟通效率。 

通过这两篇文章能够更好入门： 

[基于编程、绘画、写作的 AI 探索与总结](https://github.com/phodal/ai-research/):理解 Prompt:如何更好地完善 prompt，实现 prompt 工程就是：如何更好地使用 AI 的基础。 

[Prompt 编写模式：如何将思维框架赋予机器](https://github.com/phodal/prompt-patterns/):介绍一系列的 Prompt 编写模式，以更好地应用 Prompt 对 AI 进行编程。 


## 下面是前面吐槽AI 对话过程 {#下面是前面吐槽ai-对话过程}

<div class="ai">

[SYS]: swift 开发者 

ME: 使用PathKit 把文本添加到文件的最后一行 

AI: 使用PathKit要先安装PathKit库，可以使用CocoaPods进行安装，然后参考以下代码： 

```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
  import PathKit

  let path = Path("/path/to/file.txt")
  let text = "Hello, PathKit!"

  do {
      try path.append(text) //将文本添加到文件的最后一行
  } catch let error {
      print(error)
  }
```

这里我们使用了PathKit的\`Path\`类来操作文件，通过\`append\`方法将文本添加到文件的末尾。 

ME: Value of type 'Path' has no member 'append' 

AI: 抱歉，我的错误，PathKit库没有\`append\`方法，因此我们需要使用其他方法将文本添加到文件的末尾。以下是一个使用\`FileHandle\`进行操作的例子： 

```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
  import PathKit

  let path = Path("/path/to/file.txt")
  let text = "Hello, PathKit!"

  do {
      let fileHandle = try FileHandle(forWritingTo: path.url)
      fileHandle.seekToEndOfFile()
      if let data = text.data(using: .utf8) {
          fileHandle.write(data)
      }
      fileHandle.closeFile()
  } catch let error {
      print(error)
  }
```

这里我们打开文件的句柄，将文件指针移动到文件末尾，然后写入文本数据并关闭句柄。 

ME: 还有其他结局吗 

AI: 使用\`OutputStream\`可以将文本内容追加到文件的最后一行。以下是一个使用\`OutputStream\`进行操作的例子： 

```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
  import PathKit

  let path = Path("/path/to/file.txt")
  let text = "Hello, PathKit!\n"

  do {
      if let outputStream = OutputStream(toFileAtPath: path.string, append: true) {
          outputStream.open()
          if let data = text.data(using: .utf8) {
              outputStream.write(data: data, maxLength: data.count)
          }
          outputStream.close()
      }
  } catch let error {
      print(error)
  }
```

这里我们创建了一个\`OutputStream\`对象，将它打开并指定添加选项为\`true\`，然后写入文本数据并关闭流。需要注意的是，在文本结尾添加一个换行符，以确保将文本追加到文件的最后一行。

</div>

