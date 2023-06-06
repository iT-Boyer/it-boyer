+++
title = "使用forge工具管理issue支持多平台或代理服务"
description = "总结forge常用技巧，熟练使用，养成emacs管理和阅读issues"
date = 2021-08-10T14:42:00+08:00
publishDate = 2021-08-10T14:01:00+08:00
lastmod = 2021-08-10T17:32:27+08:00
tags = ["ARCHIVE"]
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## 目的 {#目的}

过度依赖github，长期受网速问题的困扰，尝试使用forge工具，通过接口，将issues暂存到本地，快速预览和管理issues  
在使用过程中遇到了几个问题：  

1.  使用 `hub.fastgit.org` 代理，导致forge提示不支持
2.  如何不clone库文件，直接在本地查看A库中的issues的操作


## forge添加平台域名支持 {#forge添加平台域名支持}

当在 `.gitconfig` 中替换域名的情况下:  

{{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
[url "https://hub.fastgit.org/"]
   insteadof = https://github.com/
{{< /highlight >}}

这是使用 forge 会提示一下问题：  
`Cannot determine forge repository. https://hub.fastgit.org/iT-Boyer/hugo.git 1 isn’t a forge url`  

这种情况是forge不支持未知域名，如果要正常使用就需要手动配置 `forge-alist` ，告知forge。  

例如要新增 `hub.fastgit.org` 域名，它作为github.com的代理服务，调用的方法和github.com一致： `forge-github-repository` 。  

1.  在doom.d私有配置 `config.el` 添加域名到 `forge-alist` 配置中：  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (after! forge
         (add-to-list 'forge-alist
                      '("hub.fastgit.org" "api.github.com" "hub.fastgit.org" forge-github-repository)
                      )
                  )
    {{< /highlight >}}
    
    让后，执行 `Ctrl+x Ctrl+e` 或 `spc h r r` 使配置生效。  
    
    确认添加成功 `M-x：spc h v` 搜索 `forge-alist` 。
2.  使用forge管理issues  
    可以愉快的使用常用的操作：添加 ： `forge-add-repository`  
    列表： `forge-list-repositories`  
    查看： `forge-visit-repository`  
    pull： `forge-pull`


## 使用forge拉去issues {#使用forge拉去issues}

实际场景：在库A中访问库B的issues，但是不clone 库B  

1.  添加库B到forge中：forge-add-repository 库B
2.  进入forge库列表：forge-visit-repositories return
3.  在forge库B中执行：forge-pull 完成之后就可以看到issues列表。

想在其他库目录下例如：库A中，访问库B的issues，只需要执行forge-visit-repositories 切换到库B。  

结论：forge仅依赖forge-add-repositories库设置，其默认支持在git库的目录下获取该库的issues。
