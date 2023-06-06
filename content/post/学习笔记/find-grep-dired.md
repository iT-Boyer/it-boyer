+++
title = "find-grep-dired在目录中批量替换字符串"
description = "搜索目录中的字符串并替换"
date = 2021-09-09T18:19:00+08:00
publishDate = 2021-09-09T18:07:00+08:00
lastmod = 2021-10-13T12:43:29+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

## 目的 {#目的}

学习emacs 中搜索和替换字符串的方法  


## 使用find-grep-dired {#使用find-grep-dired}

在指定的目录中批量替换字符串的操作流程  

[EmacsWiki: Dired Search And Replace](https://www.emacswiki.org/emacs/DiredSearchAndReplace)  

例如：在文件夹/dirA下替换str1为str2  

operation:  M+x ---> find-grep-dired ---> str1 ----> t ----> Q ----> str1 ---> str2 ---> 逐个确认是否替换；  

替换完成后保存替换： Ctrl + x --> s  

notice： ^ 逆向搜索，只能在本文件中进行，无法跨文件  

doom 下，快捷键绑定  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
;;绑定快捷键
(map! :leader
      :desc "搜索替换文本" "sg" #'find-grep-dired) ;; 这个快捷键是SPC s g
{{< /highlight >}}


## 全文替换 {#全文替换}


#### 一字字替换：query-replace {#一字字替换-query-replace}


#### 正则替换： replace-regexp {#正则替换-replace-regexp}


## 搜索 {#搜索}


#### 文件名搜索 {#文件名搜索}

find-dired  

find-grep-dired 递归所有子目录  

grep 在当前目录中查找指定的 regexp  


#### 文本搜索替换 {#文本搜索替换}

grep-find 显示包含字段的行，递归子目录
