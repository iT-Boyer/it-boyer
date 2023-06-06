+++
title = "org-mode和google日历同步和相关技巧"
description = "同步日历实现，在手机上管理日常事务"
date = 2021-08-24T00:00:00+08:00
publishDate = 2021-08-21T00:00:00+08:00
lastmod = 2021-08-27T12:47:53+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [目的](#目的)
- [在doom上开启google工具](#在doom上开启google工具)
- [ox-trello和gcal同步协作](#ox-trello和gcal同步协作)

</div>
<!--endtoc-->


## 目的 {#目的}

通过gtd 提高执行力。相关的技巧工具： org-roam agenda org-gcal 谷歌日历APP创建申请谷歌日历API 密钥获取谷歌日历id 获取谷歌订阅路径在iCalendar 日历中订阅在emacs中使用cfw浏览任务在gtd 中同步trello board card 在gtd 中同步谷歌日历在gtd 中添加谷歌事件，使用snippet:gcal 使用方法：add-to-default-calendar/add-to-fournal-calendar 在gtd 中添加trello 事件  


## 在doom上开启google工具 {#在doom上开启google工具}

1.  开启在 init.el app calendar 开启日历工具。
2.  配置[kidd/org-gcal.el: Org sync with Google Calendar.](https://github.com/kidd/org-gcal.el) 在goole中申请日历API访问权限  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (use-package! org-gcal
         :ensure t
         :config
         (setq org-gcal-default-calendar "test@gmail.com"
               org-gcal-client-id "xxxxxx.apps.googleusercontent.com"
               org-gcal-client-secret "xxxx-Mvpvxxxxx0Bzz"
               org-gcal-fetch-file-alist '(
                                           (org-gcal-default-calendar .  "~/org/gtd.org")
                                           ;;("another-mail@gmail.com" .  "~/task.org")
                                           ))
         )
    {{< /highlight >}}
3.  添加辅助方法  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (defun add-to-calendar ()
         (interactive)
         (org-set-property "calendar-id" org-gcal-default-calendar)
         (org-gcal-post-at-point))
    {{< /highlight >}}
4.  添加hook  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       (add-hook 'org-agenda-mode-hook (lambda () (org-gcal-sync) ))
       (add-hook 'org-capture-after-finalize-hook (lambda () (org-gcal-sync) ))
    {{< /highlight >}}
5.  禁用hook org-gcal会自动添加事件，导致在每次capture完成时，都会执行 `org-gcal--capture-post` 方法，导致不必要的警告提示等。  
    
    {{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       ;禁止gcal的capture完成时的同步事件
       (remove-hook! 'org-capture-after-finalize-hook  '(org-gcal--capture-post))
    {{< /highlight >}}


## ox-trello和gcal同步协作 {#ox-trello和gcal同步协作}

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
(add-hook 'org-capture-after-finalize-hook (lambda () (org-trello-sync-buffer) ))
{{< /highlight >}}
