+++
title = "学习创建自己的SPM新增库集合package-collection"
description = "学习SPM新增包集合，管理常用包"
date = 2021-08-20T14:08:00+08:00
publishDate = 2021-08-20T13:28:00+08:00
lastmod = 2021-09-07T15:06:57+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [目的](#目的)
- [制作](#制作)

</div>
<!--endtoc-->


## 目的 {#目的}

学习swift集成库的管理方法，掌握包集合的制作和使用方法  

主要 [Swift.org - Package Collections](https://swift.org/blog/package-collections/) [Swift 新闻之 Package Collections 是什么 - SwiftUI自学网站](https://www.openswiftui.com/?p=1755)  

包集合 主要使用json文件整理和多个包的信息，制作过程中需要通过swift-package-collection-generator工具辅助完成。  

使用：添加到自己的项目中的两种方式：  

方式一：xcode13 新增的功能  

方式二：spm命令：swift package-collection add url  


## 制作 {#制作}

1.  安装生成器工具：swift-package-collection-generator `package-collection-generate` 命令没有在swift命令集中，需要clone [源码](https://github.com/apple/swift-package-collection-generator) ，编译成可执行文件.  
    
    {{< highlight shell "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       git clone swift-package-collection-generator.git
       cd swift-package-collection-generator
       swift build
       #安装
       cp .build/x86_64-apple-macosx/debug/package-collection-generate ~/../bin
    {{< /highlight >}}
    
    我使用dotfiles方式管理本地环境，所以安装时，只要把可执行文件cp到相应的bin目录即可。在终端加载之后，可以方便在其他地方快速调用。
2.  新建json 我想整理swift-sh终端开发经常用到的几个库，就可以新建一个json文件 `swift-sh` ，添加库的路径即可。  
    
    {{< highlight json "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       {
           "name": "终端工具依赖库",
           "packages": [
               {
                   "url": "https://github.com/apple/swift-argument-parser.git"
               },{
                   "url": "https://github.com/tuist/XcodeProj.git"
               },{
                   "url": "https://github.com/SwiftyJSON/SwiftyJSON.git"
               },{
                   "url": "https://github.com/sharplet/Regex.git"
               },{
                   "url": "https://github.com/iT-Boyer/AlfredSwift.git"
               },
               {
                   "url": "https://github.com/pvieito/PythonKit"
               }
           ]
       }
    {{< /highlight >}}
3.  生成标准json 生成包集合的命令： `package-collection-generate json源文件 collection.json`  
    
    {{< highlight shell "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       package-collection-generate packages.json collection.json
    {{< /highlight >}}
4.  部署到远程  
    
    使用githubpage作为在线服务，放在私有项目的文档库:iDocs中，  
    
    服务路径： `https://it-boyer.github.io/iDocs/`  
    
    把上一步生成的标准包集合json文件，放在idocs目录下的 `SPM-JSON/swift-sh` 目录中。  
    
    让后既可以提交部署到githubpage服务了。
5.  使用  
    
    路径区分大小写。  
    
    通过命令：  
    
    {{< highlight shell "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       swift package-collection add https://it-boyer.github.io/iDocs/SPM-JSON/swift-sh/collection.json
    {{< /highlight >}}
