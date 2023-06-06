+++
title = "AI协作doom集成第三方插件"
description = "博客简介"
date = 2023-04-18T11:48:00+08:00
lastmod = 2023-04-18T11:53:43+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

基于doom的办公环境，在集成第三插件时，经常遇到配置方式不兼容的情况，对 `elisp` 语法不求甚解的使用者，在集成过程非常煎熬。 

比如在集成org-sync插件时，该插件处于停止维护状态，配置加载脚本使用的 `mapc ‘load` 语法，没有太多经验的话，github issue 又无人问津的时候，就很能错过一个很好工具。 

如果将这个问题跑给AI，问题就迎刃而解了。 


## AI助手 {#ai助手}

Me:如何把以下方式转为doom转为 doom 中 使用use-package! 加载: 

```elisp
(mapc 'load
      '("org-sync" "org-sync-bb" "org-sync-github" "org-sync-redmine"))
```

AI: 

> 在Doom Emacs中,我们可以使用\`use-package!声明来加载Emacs库文件,代替上例中的mapc\`和\`load\`。相比直接加载,use-package!有以下优点: 
> 
> 1.  可以指定库的详细信息,如名字、描述、作者等
> 2.  可以指定加载条件、安装脚本、初始化设置等
> 3.  加载过程更加结构化,易于管理和修改
> 
> 那么,上例使用\`use-package!\`重写如下: 
> 
> ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
> (use-package! org-sync
>   :load "org-sync")
> 
> (use-package! org-sync-bb
>              :load "org-sync-bb")
> 
> (use-package! org-sync-github
>              :load "org-sync-github")
> 
> (use-package! org-sync-redmine
>              :load "org-sync-redmine")
> ```
> 
> 这里,我们为每个库文件声明一个\`use-package!块,并使用:load\`参数指定其路径以加载。 use-package!会自动处理库文件的安装、加载和初始化设置。我们只需要声明库文件的基本信息,它就会采取默认行为完成加载。当然,我们也可以进一步自定义: 
> 
> -   使用\`:demand\`指定加载条件
> -   使用\`:if\`和\`:unless\`指定加载环境
> -   使用\`:mode\`指定特定模式下加载
> -   使用\`:commands\`指定提供的命令
> -   使用\`:init\`完成初始化设置
> -   等等
> 
> 所以,使用\`use-package!\`重写上例的主要步骤是: 
> 
> 1.  为每个库文件声明一个\`use-package!\`块
> 2.  使用\`:load\`指定库文件的路径以加载
> 3.  use-package!会自动处理加载和初始化
> 4.  进一步自定义加载条件、命令、设置等(可选)
> 
> use-package!使包管理和装载过程更加灵活简洁。相比直接使用\`load\`加载,它提供了更高级的自定义选项,使我们的配置文件更加结构化和易维护。希望这个例子能帮助您进一步理解\`use-package!\`的妙用。如果您有任何其他问题,欢迎在回复中提出。我很乐意与您探讨Doom Emacs及相关技巧! 

