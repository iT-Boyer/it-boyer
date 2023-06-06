+++
title = "hugo导出roam链接失败相关问题总结"
description = "通过分析bug，明确问题的真正原因，总结并吸收经验"
date = 2021-10-18T18:44:00+08:00
publishDate = 2021-10-18T16:41:00+08:00
lastmod = 2021-10-18T18:45:07+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

## I:辨别知识和信息 {#i-辨别知识和信息}

在hugo导出过程中，文中如果出现roam链接，导出会出现问题。  

目前现象，如果roam链接的节点在同一个文件时，可以导出成功。如果roam链接的节点和hugo博客不在同一个org文件中时，导出就会失败。  

由于目前使用的roam2 版本，找到两个roam1解决方案，通过hook方法替换链接方式尝试解决，结果在roam2 中失败。  

{{< highlight log "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
Error (after-save-hook): Error running hook "org-hugo-export-wim-to-md-after-save" because: (org-link-broken 8bf3df1d-b8f3-4e26-99fb-da0bc476771f)
{{< /highlight >}}


### 方案一 {#方案一}

[Updating backlinks of the destination post](https://github.com/org-roam/org-roam/issues/773)  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
(defun jethro/org-roam-export-updated ()
    "Re-export files that are linked to the current file."
    (interactive)
    (let ((files (emacsql-sqlite [:select [to] :from links :where (= from $s1)] buffer-file-name)))
      (dolist (f files)
        (with-current-buffer (find-file-noselect (car f))
          (org-hugo-export-wim-to-md)))))

(add-hook! 'after-save-hook 'jethro/org-roam-export-updated)
{{< /highlight >}}

在上文中用到的org-roam-sql 已经废弃，替换为 `emacsql-sqlite`  

新问题： `Error (after-save-hook): Error running hook "jethro/org-roam-export-updated" because: (error Keyword argument /Users/boyer/hsg/iNotes/content-org/all-posts.org not one of (:debug))`  

[On Android Termux, any access to the database fails with "middleware out of memory" error](https://github.com/org-roam/org-roam/issues/1605)  

[emacsql-sqlite returns "middleware out of memory" on Termux (Android)](https://github.com/skeeto/emacsql/issues/78)  


### 方案二 {#方案二}

[Exporting Org Roam notes to hugo - Sidharth Arya](https://sidhartharya.me/exporting-org-roam-notes-to-hugo/#comments)  

该方案针对roam1 版本，针对文件来导出roam 连接，经验证部分方法（ `org-roam--org-roam-file-p` ）已废弃：  

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
;替换链接
(defun org-hugo--org-roam-backlinks (backend)
  (when (equal backend 'hugo)
  (when (org-roam--org-roam-file-p)
    (beginning-of-buffer)
    (replace-string "{" "")
    (beginning-of-buffer)
    (replace-string "}" "")
    (end-of-buffer)
    (org-roam-buffer--insert-backlinks))))
(add-hook 'org-export-before-processing-hook #'org-hugo--org-roam-backlinks)

;添加hook方法保存
(defun org-hugo--org-roam-save-buffer(&optional no-trace-links)
  "On save export to hugo"
  (when (org-roam--org-roam-file-p)
      (org-hugo-export-wim-to-md)))
(add-to-list 'after-save-hook #'org-hugo--org-roam-save-buffer)
{{< /highlight >}}


## A1:激活/追问反思经验 {#a1-激活-追问反思经验}

现在把博客分为了几个文件来管理  

habit主要记录习惯和思维模式的转换相关总结，  

all-post主要记录博客项目，导出时专门对应ox-hugo做了类别分类，可以很好的处理博客的管理。  

project:项目级别的主要放在project文件中管理，它的特色时tj3工具的集成，对管理项目有很好的兼容。  

在这三大类的处理之后，出现roam链接导出的问题。失去了很重要的一项机制。如果导出问题解决不了，就要考虑在一个文件中统一管理三个角色，充分应用roam机制。  


## A2:内化应用知识，谱写愿景 {#a2-内化应用知识-谱写愿景}

目的：一定要激活roam的使用。  

方案一要使用两个小时来解决导出导出链接问题。  

方案二：把项目重新整合到一个文件中来管理博客，习惯，项目。要使用roam-ui回顾项目，把项目做到卡片机制，做到知识二次应用。  


## 要事第一，制定下一步行动 {#要事第一-制定下一步行动}

目前使用方案是放在同一个文件中管理每分类。
