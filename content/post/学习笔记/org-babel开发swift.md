+++
title = "AI 解放 IDE ，专注 emacs 提升生产力"
description = "博客简介"
date = 2023-04-19T21:05:00+08:00
lastmod = 2023-04-20T08:22:52+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

和 AI 对话一段时间，受到很大的启发。 

在 AI 辅助下，日常开发，时间和项目管理，效率都有了明显的提升。相信 AI 将在生活中扮演更多角色，影响自己的行为习惯。 

网上看到已经有大牛，用 AI 实现了无代码开发，从项目框架设计，功能开发和测试发布，都由 AI 一步步回答实现，例如 mygptreader 。 

受此启发，在尝试开发过程中，尽量使用 AI 来完成，功能的设计和开发。 

例如，使用 swift 开发一个 snippet 转换插件过程中，只需负责提需求，验收 AI 代码可用性，然后集成到脚本运行就好。 

这样以来，任务重心偏向了需求挖掘，功能设计，代码验收上。做这些工作，更多体现在领导和管理上，如何扮演好新的角色至关重要。 

emacs 拥有丰富的插件，在 `org-AI` 的加持下， `tj3` 项目管理，博客写作 `ox-hugo` 插件， `plantuml` 插件， `代码块` 插件等，都能有效的提升生产力。经过这次 AI 浪潮洗礼，将 emacs 训练成高效能七个习惯的执行者的愿望，也许指日可待了。 


## 使用 AI 制作 prompt snippets 提升 chatgpt 沟通 {#使用-ai-制作-prompt-snippets-提升-chatgpt-沟通}

使用脚本收集流行的 prompt 库，批量转换 snippet， 例如：alfred snippet ， emacs snippet 等。 

通过这个简单脚本的开发，借助 AI 尝试无代码编程。在需求，设计，编码，调试实战中，工作中心偏设计管理，尽量在 emacs 中探索可提升产出的技术栈，学习积累新的开发习惯。 

技术设计：采用 swift 语言， `JSON` 库解析 propmt 源数据转为目标 snippt 格式。 

开发工具：借助 emacs 插件提升产出效率。例如：利用 `org-swift` `plantuml` 快速设计，测试等。 


### 分析数据模型 {#分析数据模型}

1.  prompt 数据源样例 
    
    这段 json 片段来自微信机器人 chat-on-wechat 中的角色插件，插件中整理很多关于角色 prompt。 
    
    主要功能：例如在片段的角色的描述，在微信中和 AI 聊天时，只要输入 `佛祖` 即可。 
    
    制作 snippet 会用到 title ， descan remark 字段。 
    ```json { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       {
           "title": "佛祖",
           "description": "从现在开始你是佛祖，你会像佛祖一样说话。你精通佛法，熟练使用佛教用语，你擅长利用佛学和心理学的知识解决人们的困扰。你在每次对话结尾都会加上佛教的祝福。",
           "descn": "从现在开始你是佛祖，你会像佛祖一样说话。你精通佛法，熟练使用佛教用语，你擅长利用佛学和心理学的知识解决人们的困扰。你在每次对话结尾都会加上佛教的祝福。",
           "wrapper": "您好佛祖，我：\"%s\"",
           "remark": "扮演佛祖排忧解惑",
           "tags": [
               "interesting"
           ]
       },
    ```
2.  snippet 目标结构样例： emacs snippet 格式 
    
    这段显示的 `or-ai` 的 snippet 结构展示，作为脚本的输出格式。 
    
    snippet 的 key 使用 prompt 名称的中文全拼缩写。 
    
    例如：将 `佛祖` prompt 转为 snippet 效果： 
    ```snippet { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
         # -*- mode: snippet -*-
         # name: 佛祖
         # key: fz
         # --
         #+begin_ai markdown :max-tokens 250
         ${1:[SYS]: ${2:从现在开始你是佛祖，你会像佛祖一样说话。你精通佛法，熟练使用佛教用语，你擅长利用佛学和心理学的知识解决人们的困扰。你在每次对话结尾都会加上佛教的祝福。}
    
         }[ME]: 您好佛祖，我：
         $0
         #+end_ai
    ```


### emacs + AI 开发模式 {#emacs-plus-ai-开发模式}

在 org 中进行 swift 语言开发，主要用到一下插件： 

1.  `org-ai` 在 org 中 AI 实时对话
2.  `org-swift` ，支持 swift 脚本 org-babel ，在 org 中轻松运行脚本，便于测试验收代码，类似 swift playgroud 。 
    
    采用字符串的形式，处理格式转换，然后写入目标文件保存到文件 
    
    1.  用变量模拟 json 数据
    2.  采用多行字符串组装要转换的格式
    3.  使用 print 验证字符串结果
    
    <!--listend-->
    
    ```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       var name = "miao"
       var key = "mn"
       var prompt = "猫娘"
       let snippet = """
         # -*- mode: snippet -*-
         # name: \(name)
         # key: \(key)
         # --
         #+begin_ai markdown :max-tokens 250
         ${1:[SYS]: ${2:\(prompt)}
    
         }[ME]: $0
         """
       print(snippet)
    ```
    打印结果： 
    
    ```text
    # -*- mode: snippet -*-
    # name: miao
    # key: mn
    # --
    #+begin_ai markdown :max-tokens 250
    ${1:[SYS]: ${2:猫娘}
    
    }[ME]: $0
    ```


### 获取中文全拼缩 {#获取中文全拼缩}

```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
import Foundation
func PYFirst(string:String?, _ allFirst:Bool=false)->String{
    var py="#"
    if let s = string {
        if s == "" {
            return py
        }
        let str = CFStringCreateMutableCopy(nil, 0, s as CFString)
        CFStringTransform(str, nil, kCFStringTransformToLatin, false)
        CFStringTransform(str, nil, kCFStringTransformStripCombiningMarks, false)
        py = ""
        if allFirst {
            for x in (str! as String).components(separatedBy:" ") {
                py += PYFirst(string:x)
            }
        } else {
            py  = (str! as NSString).substring(to: 1).uppercased()
        }
    }
    return py
}

//调用示例，返回#
var s :String?
//PYFirst(string:s)

s = "中华人民共和国@hi wor\r\nld."
//调用示例，返回ZHRMGHGW
let result =  PYFirst(string:s,true)

//调用示例，返回Z
let result2 = PYFirst(string:s)
print("结果1: \(result) \n 结果2: \(result2)")
```

```text
结果1: ZHRMGHGW
 result2: Z
```


### 脚本的核心功能和运用 {#脚本的核心功能和运用}

使用 JSONEncoder 解析本地的 json 文件，转为 model 对象。 

目前支持三个工具的 snippet 

1.  alfred snippet 
    
    转为 json 文件集合，需要手动压缩成 `.zip` ，然后命名为 `.alfredsnippets` 
    
    如果安装无效，尝试以下方式安装 
    
    1.  在alfred snippet 新建一个snippet 集合，然后导出为 `alfredsnippets` 包
    2.  再使用 zip 工具不解压缩的情况下，将生成的 json 导入到 包中。
    3.  双击包，成功导入到 alred snippet 中。
2.  emacs-snippet
3.  auto-gpt yaml模板 
    
    使用方法：python -m auto-gpt --settings-file role.yaml
4.  生成chatfred 的aliases 
    
    安装方法：把chatfred的内容拷贝到chatfred 配置项中

<!--listend-->

```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }

public static func toSnippets(json:String) -> String? {
        // 使用 JSONEncoder 解析本地的json 文件，转为model 对象。
        let url = URL(fileURLWithPath: json)
        let data = try! Data(contentsOf: url)
        let prompt = try! JSONDecoder().decode(Prompt.self, from: data)
        prompt.roles.map { role in
            // 保存为json 文件
            var wrapper = role.wrapper
            wrapper.replaceAll(matching: "\n.*$", with: "")
            do {
                // alfred -----
                let jsonEncoder = JSONEncoder()
                let alfred = SnippetModel(uid: UUID().uuidString,
                                          name: role.title,
                                          keyword: role.title.pinyin.lowercased(),
                                          snippet: role.descn)
                let alfredM = AlfredModel(alfredsnippet: alfred)
                jsonEncoder.outputFormatting = .prettyPrinted
                let resultData = try jsonEncoder.encode(alfredM)
                let filename = alfred.name + " [\(alfred.uid)]"
                let newfile = "/Users/boyer/Desktop/propmt/\(filename).json"
                let url = URL(fileURLWithPath: newfile)
                try resultData.write(to: url, options: .atomic)

                // emacs-snippet ---把字符串保存为文件 ---
                let snippet = """
                    # -*- mode: snippet -*-
                    # name: \(role.title)
                    # key: \(alfred.keyword)
                    # group: \(role.tags.first ?? "")
                    # --
                    #+begin_ai markdown :max-tokens 250
                    [SYS]: \(role.descn)

                    [ME]: \(wrapper)
                    $0
                    #+end_ai
                    """
                let snippetfile = ".doom.d/snippets/org-mode/ai/\(alfred.name)"
                let emacsnippet = Path.home+snippetfile
                try! emacsnippet.write(snippet)

                //auto-gpt -----
                //使用方法：python -m auto-gpt --settings-file role.yaml
                let auto_gpt = """
                    ai_goals:
                    - \(role.remark)
                    ai_name: \(role.title)
                    ai_role: \(role.descn)
                    """
                let autofile = ".dotfiles/auto-gpt/roles/\(role.title).yaml"
                let autogpt = Path.home+autofile
                try! autogpt.write(auto_gpt)

                //生成chatfred 的aliases
                //使用方法：把chatfred的内容拷贝到chatfred 配置项中。
                let alfred_aliases = role.title.pinyin.lowercased() + "=" + role.descn + ";\n"
                let fred = "Desktop/chatfred.txt"
                let chatfred = Path.home+fred
//                try! chatfred.append(alfred_aliases)
                let fileHandle = try FileHandle(forWritingTo: chatfred.url)
                    fileHandle.seekToEndOfFile()
                    if let data = alfred_aliases.data(using: .utf8) {
                        fileHandle.write(data)
                    }
                    fileHandle.closeFile()
            } catch {
                print("生成失败")
            }
        }
        return "生成成功"
    }
```


## <span class="org-todo todo TODO">TODO</span> ob-babel 导入第三库遇到的问题 {#ob-babel-导入第三库遇到的问题}

问题1. org-mode 9.6.1 在使用 var 传递变量失败 

1.  在导入第三方库时，通过 ai 提供了几种方法，验证都无效 
    
    1.  使用：var myLibrary="Regex" 在swift 块中，import $myLibrary
    2.  使用：var loadpath ="" 源码路径，在 emacs-elisp 块中夹带 load-path 方式
    3.  在 swift 块中使用 load-file 的方式加载 swift 文件，然后使用库方法
    
    三种方法在验证之后都是无效的。

<!--listend-->

```emacs-lisp
(setq load-path (cons includepath load-path))
(load-file (concat includepath "Regex.swift"))
```

```swift
let myString = $myString  // 将Org-mode代码块中的myString变量赋值给Swift变量
print("声明的变量：\(myString)")
let pattern = "T.*T"
let regex = try! Regex(pattern)
if regex.matches(myString) {
    print("Regex matched!")
} else {
    print("Regex didn't match.")
}
```

```text
<stdin>:1:16: error: cannot find '$myString' in scope
let myString = $myString  // 将Org-mode代码块中的myString变量赋值给Swift变量
<stdin>:5:10: error: value of type 'Regex<AnyRegexOutput>' has no member 'matches'
if regex.matches(myString) {
   ~~~~~ ^~~~~~~
```

```swift
import Foundation
var text = "test\n \"%s\""
print("kkkk==\(text)")
//text.removeLast(4)//replacingOccurrences(of: "\"%s\"", with: "")
text.replaceAll(matching: "%.*$", with: "")
print("jjjj==\(text)")
```

```text
<stdin>:5:6: error: value of type 'String' has no member 'replaceAll'
text.replaceAll(matching: "%.*$", with: "")
~~~~ ^~~~~~~~~~
```

