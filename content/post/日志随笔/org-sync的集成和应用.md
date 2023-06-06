+++
title = "使用org-sync同步github issue功能"
description = "博客简介"
date = 2023-04-18T09:58:00+08:00
lastmod = 2023-04-19T13:55:43+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

如何同步org到github issues的需求，终于在AI的帮助下，确定了使用 org-sync 来实现。 

这篇文章主要记录：如何集成到doom 中，调试运行org-sync功能，同步测试githubissues。 

借助AI 快速实现这个过程，估算半个小时完成调试，然后发布这篇博客。 


## 从AI助手开篇 {#从ai助手开篇}

先弄清楚两件事情：什么是org-sync，如何集成到doom中 

1.  org-sync 是什么，来自AI解释 
    
    [Claude] 
    
    > Org-sync是一个方便同步多个org文件或org文件与其他格式文件的Emacs插件。它的主要功能和应用技巧如下: 功能: 
    > 
    > 1.  同步org文件:可以在多个org文件之间同步标题,标签,属性,TODO状态等。这在维护大型项目或知识库时很有用。
    > 2.  org文件与其他格式文件同步:目前支持与Markdown,HTML,LaTeX等格式的同步。你可以在org文件和这些文件之间同步内容与格式。
    > 3.  同步粒度灵活:可以同步整个文件,指定的标题树,甚至是节点属性。并提供同步模式选项,可以同步新增/修改或删除的内容等。
    > 4.  同步方式多样:手动同步、自动同步、基于文件修改时间的同步等。可以选择最适合自己的同步触发方式。
    > 5.  同步模板:提供同步模板功能,可以根据模板快速初始化同步配置,非常方便。
    > 6.  第三方服务集成:支持与Dropbox,Nextcloud等文件托管服务的集成。可以实现跨设备的org文件自动同步。
    > 
    > 应用技巧: 
    > 
    > 1.  用作大项目的入口文件:在主org文件中同步所有子文件与部分,统一查看与管理项目。
    > 2.  知识管理:在一个总览org文件中同步你所有的org知识库或大纲文件,全面掌握自己的知识体系。
    > 3.  博客撰写:在org文件中撰写博客或书籍内容,然后同步至Markdown等格式,并发布在对应的平台上。
    > 4.  重复内容处理:如果你有多个文件中出现重复的结构或内容,那么同步这些文件是一种简便处理重复的好办法。
    > 5.  跨设备同步:结合文件托管服务,实现无缝同步至其他设备,并继续在其上工作或查看。
    > 6.  格式转换:如果需要将org内容输出为其他格式,同步功能可以省去手动导出的步骤,轻松转换您的格式。

2.  如何集成到doom中 
    
    org-sync 没有针对doom集成的教程，可以借助AI 了解一下如何将一个插件安装到doom，并添加相关配置，然后写出自己的配置： 
    
    Me: 如何把org-sync插件安装到doom中。 
    
    Claude: 
    
    > 在Doom Emacs中安装org-sync插件的步骤如下: 
    > 
    > 1.  确认org-sync支持安装在Doom Emacs中。目前该插件可以通过下面两种方式安装:
    > 2.  MELPA 仓库:Doom Emacs默认支持MELPA,所以可以直接安装
    > 3.  手动安装:下载org-sync.el文件,并配置路径等,Doom Emacs也支持手动安装插件
    > 
    > 所以,org-sync是可以在Doom Emacs中使用的。 
    > 
    > 1.  在Doom Emacs的配置文件中启用org-sync插件。
    > 
    > 你需要编辑~/.doom.d/config.el文件,并在其中添加: elisp (package! org-sync)  ;; 安装org-sync包这会告诉Doom在启动时从MELPA仓库安装org-sync插件。 
    > 
    > 1.  重启Emacs或重新加载配置以启用org-sync插件。
    > 
    > 在Doom提示符下输入SPC q r 重启Emacs,或输入 SPC q l 重新加载配置。这会重新加载配置,并安装启用org-sync插件。 
    > 
    > 1.  (可选)对org-sync进行额外配置。
    > 
    > 如果需要,你可以对org-sync插件的设置及键绑定进行定制配置。在~/.doom.d/config.el文件中添加类似以下内容: elisp (use-package! org-sync :config ;; 您的org-sync配置 ) 
    > 这会在org-sync加载后运用你的配置。 
    > 
    > 1.  使用 M-x org-sync-setup 在需要同步的org文件中进行配置,并开始使用org-sync插件!
    > 
    > org-sync的使用自此开始,你可以通过自定义键绑定 SPC m s 或 M-x org-sync 等访问其功能。以上步骤可以帮助你在Doom Emacs中成功安装与使用org-sync这个方便的org文件同步插件。如果在安装与配置的过程中有任何问题,也请告知我,我会提供更详细的帮助。


## 开始编写自己配置 {#开始编写自己配置}

> github版本:[arbox/org-sync: Issue Tracking Synchronization for Emacs Org-mode.](https://github.com/arbox/org-sync) 
> 
> 旧版本：[~bzg/org-sync - {ARCHIVE} Synchronize online bugtrackers with org documents -...](https://git.sr.ht/~bzg/org-sync) 

org-sync 有两个版本：都处于停止维护状态，最终以github版本为主，它fork 另一版本做了大量优化，导致两个版本的使用文档的描述不一致。 

1.  新建一个模块：org-sync，得到 `config.el`, `packages.el`
2.  在添加配置，集成额时gitbub版 package.el ： `(package! org-sync)`
3.  配置config.el 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (use-package! org-sync
         :load-path "org-sync")
    
       (use-package! org-sync-bb
                    :load-path "org-sync-bb")
    
       (use-package! org-sync-github
                    :load-path "org-sync-github")
    
       (use-package! org-sync-redmine
                    :load-path "org-sync-redmine")
       ;; 添加其他后台
    ```


## 调试运行同步 {#调试运行同步}

按照官方文档，提供了一个测试[GitHub - ostesting/test: test repo for org-sync](https://github.com/ostesting/test)库，用来测试同步效果。 orbox版本的org-sync 提供了两个交互命令： `org-sync` 同步当前item `org-sync-or-import` 它根据缓冲区内容同时执行这两个操作。如果缓冲区中没有要同步的内容，则调用 import，否则调用 sync。参考旧版的使用说明会更好的理解上述两个命令的由来： 

> `org-sync` 中有3个交互命令: 
> 
> 1.  `os-import` 同步当前item。
> 2.  `os-sync` 同步buffer中所有的item。
> 3.  `os` 它根据缓冲区内容同时执行这两个操作。如果缓冲区中没有要同步的内容，则调用 os-import，否则调用 os-sync。


### <span class="org-todo todo TODO">TODO</span> 尝试将改org同步到iweeks 库中 {#尝试将改org同步到iweeks-库中}


## <span class="org-todo todo TODO">TODO</span> 自定义同步其他平台 {#自定义同步其他平台}

