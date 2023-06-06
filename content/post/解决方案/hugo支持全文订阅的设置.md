+++
title = "hugo支持全文RSS设置问题"
description = "hugo默认订阅只有博客的简介内容，如果想RSS订阅全文可以看下文章。"
date = 2021-10-21T18:11:00+08:00
publishDate = 2021-10-21T13:28:00+08:00
lastmod = 2021-10-21T18:11:21+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## elfeed订阅初衷 {#elfeed订阅初衷}

在emacs中沉浸阅读体验使用elfeed插件必不可少，主要以来RSS订阅功能，可以逃离app束缚，使用喜欢的方式阅读。  

现在想通过RSS订阅自己的博客，但是会出现不是全文的情况，参看了hugo文档，设置RSS 订阅配置，就可以实现全文订阅。  


## RSSHub订阅源的流行 {#rsshub订阅源的流行}

现在支持RSS订阅功能的网站越来越少，造就了RSSHub的盛行，可以为帮助用户轻松订阅不支持RSS的王章，得益于很多人在贡献自己制作的订阅源，分享到到RSSHub网站上，提供给同样感兴趣的订阅者。  

比如前几天想通过telegram机器人实现微信公众号订阅源，但是最终以失败告终。不过学到了telegram机器人和公众号的爬去机制。  

制作微信公众号订阅源失败了，可以制作自己博客的订阅源，换个教书熟悉RSS技术，在过程中最好能够解决elfeed订阅RSSHub订阅失败的问题。  


## hugo支持全文订阅 {#hugo支持全文订阅}


### 定制rss模板 {#定制rss模板}

1.  在 `hsg/.iNotes/layouts/` 新建文件 `index.rss.xml`  
    
    核心代码行：  
    
    `<description>{{ .Content | html }}</description>`  
    
    `.Content` ： 全文  
    
    {{< highlight xml "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       {{- $pctx := . -}}
       {{- if .IsHome -}}{{ $pctx = .Site }}{{- end -}}
       {{- $pages := slice -}}
       {{- if or $.IsHome $.IsSection -}}
       {{- $pages = $pctx.RegularPages -}}
       {{- else -}}
       {{- $pages = $pctx.Pages -}}
       {{- end -}}
       {{- $limit := .Site.Config.Services.RSS.Limit -}}
       {{- if ge $limit 1 -}}
       {{- $pages = $pages | first $limit -}}
       {{- end -}}
       {{- printf "<?xml version=\"1.0\" encoding=\"utf-8\" standalone=\"yes\"?>" | safeHTML }}
       <rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
         <channel>
           <title>{{ if eq  .Title  .Site.Title }}{{ .Site.Title }}{{ else }}{{ with .Title }}{{.}} on {{ end }}{{ .Site.Title }}{{ end }}</title>
           <link>{{ .Permalink }}</link>
           <description>最近更新的内容 {{ if ne  .Title  .Site.Title }}{{ with .Title }}in {{.}} {{ end }}{{ end }}on {{ .Site.Title }}</description>
           <generator>Hugo -- iT-Boyer.github.io</generator>{{ with .Site.LanguageCode }}
           <language>{{.}}</language>{{end}}{{ with .Site.Author.email }}
           <managingEditor>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</managingEditor>{{end}}{{ with .Site.Author.email }}
           <webMaster>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</webMaster>{{end}}{{ with .Site.Copyright }}
           <copyright>{{.}}</copyright>{{end}}{{ if not .Date.IsZero }}
           <lastBuildDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</lastBuildDate>{{ end }}
           {{- with .OutputFormats.Get "RSS" -}}
           {{ printf "<atom:link href=%q rel=\"self\" type=%q />" .Permalink .MediaType | safeHTML }}
           {{- end -}}
           {{ range $pages }}
           <item>
             <title>{{ .Title }}</title>
             <link>{{ .Permalink }}</link>
             <pubDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</pubDate>
             {{ with .Site.Author.email }}<author>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</author>{{end}}
             <guid>{{ .Permalink }}</guid>
             <description>{{ .Content | html }}</description>
           </item>
           {{ end }}
         </channel>
       </rss>
    {{< /highlight >}}

2.  设置订阅限制数  
    
    `{{- $limit := .Site.Config.Services.RSS.Limit -}}`  
    
    在模板代码中有获取配置文件 `config.toml` 属性值：  
    
    {{< highlight yaml "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       # 制定RSS最大条数
       rssLimit = 10
    {{< /highlight >}}
    
    查看：[rsslimit | Hugo](https://gohugo.io/getting-started/configuration/#rsslimit)  
    
    更多：[Site 参数获取 | Hugo](https://gohugo.io/variables/site/)


### 特殊 `^H` 符号导致RSS问题 {#特殊-h-符号导致rss问题}

特殊符号，会导致一下问题：  

{{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
This page contains the following errors:
error on line 20461 at column 29: PCDATA invalid Char value 8
Below is a rendering of the page up to the first error.
{{< /highlight >}}

1.  在emacs 执行删除操作  
    
    选中并拷贝 `v y`  
    
    全文搜索： `spc s d 目录`  
    
    替换： `M-x:replace-string`  
    
    清理完成之后，重新编译即可。


## elfeed订阅RSSHub 方案和订阅源筛选 {#elfeed订阅rsshub-方案和订阅源筛选}

因为RSSHub大部分是用户分享的订阅源，存在失效情况。  

为增加订阅的成功率，可以参考RSSHub官网搭建私服服务的教程，配合RSSHub 公布的订阅源，把域名替换为私服路径，在验证是否有效。  

推荐使用heroku平台部署文档：[一键部署到 Heroku](https://docs.rsshub.app/install/#bu-shu-dao-heroku)  

已经个人的服务： `https://i2rsshub.herokuapp.com`  

验证RSS订阅源主要关注几点：  

1.  订阅内容内容质量  
    
    内容是否符合自己兴趣爱好，有质量，有新东西的网站，才能有高效阅读体验

2.  更新频率  
    
    主要为了筛选过期的订阅源，节省精力。  
    
    如果质量很好值得收藏，例如objc.io 已经停更，但是质量很高。  
    
    对于高质量的订阅源，要利用好elfeed 的时间过滤条件，也能够轻松驾驭过期期刊。

3.  支持全文订阅  
    
    如果不支持全文订阅，文章质量有很好，可以借助 `pocket-reader` 工具获取全文内容  
    
    pocket-reader 可以通过 `pocket-reader-add-link` 从org/elfeed/eww 等buffer 中的超链接，直接加入到pocket-reader 列表。  
    
    pocket-reader 提供了优秀的阅读体验，可以将订阅内容格式化为org 在 `org-mode` 模式下阅读，相当惊艳。


## pocket-reader提升阅读幸福感 {#pocket-reader提升阅读幸福感}

pocket-reader 本身不支持订阅，是通过文章URL, 抓取文章内容的机制，这样在emacs 中提升阅读体验提供了基础。  

[GitHub - alphapapa/pocket-reader.el: Emacs client for Pocket reading list (ge...](https://github.com/alphapapa/pocket-reader.el)  

掌握了这个阅读器，可以将网页内容，格式化为org 文章来阅读，这样提升阅读体验，在写博客时，也能沉浸在emacs 世界，尽情发挥想象力，全神贯注的投入创造。  

它的快捷键很多，需要切换到在 edit 模式。  

1.  关于文章来源  
    
    pocket 有很多工具的支持，几乎可以实现随时随地推送一篇博客到pocket中。  
    
    1.  浏览器插件
    
    2.  alfred工具
    
    3.  elfeed
    
    4.  微信公众号无解
