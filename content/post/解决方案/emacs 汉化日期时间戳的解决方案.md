+++
title = "emacs中汉化显示星期遇到的问题"
description = "优化emacs 中文显示问题，填坑记录一下。"
date = 2021-08-30T09:58:00+08:00
publishDate = 2021-08-27T11:52:00+08:00
lastmod = 2021-08-30T09:58:32+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

1.  设置

<!--listend-->

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
;; 设置agenda 显示中文 月、周、日
;; https://emacs-china.org/t/agenda/7711/4
(setq-default
 ;; inhibit-startup-screen t;隐藏启动显示画面
 ;; calendar-date-style 'iso
 calendar-day-abbrev-array ["周七" "周一" "周二" "周三" "周四" "周五" "周六"]
 calendar-day-name-array ["周七" "周一" "周二" "周三" "周四" "周五" "周六"]
 calendar-month-name-array ["一月" "二月" "三月" "四月" "五月" "六月" "七月" "八月" "九月" "十月" "十一月" "十二月"]
 calendar-week-start-day 1
 org-agenda-deadline-leaders (quote ("最后期限:  " "%3d 天后到期: " "%2d 天前: "))
 org-agenda-scheduled-leaders (quote ("要事:" "%2d次☞"))
;; ------时间戳汉化------
 system-time-locale "zh_CN.UTF-8" ;; "C":英文格式
 org-time-stamp-formats  '("<%Y-%m-%d 周%a>" . "<%Y-%m-%d 周%a %H:%M>")
 org-display-custom-times t
 org-time-stamp-custom-formats '("<%Y-%m-%d 周%a>" . "<%Y-%m-%d 周%a %H:%M>")
 org-deadline-warning-days 5;;最后期限到达前5天即给出警告
 )
{{< /highlight >}}

1.  测试脚本

<!--listend-->

{{< highlight elisp "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
;;系统时间设为英文
;;(setq system-time-locale "C")
(setq system-time-locale "zh_CN.UTF-8")
(format "系统环境：%s" system-time-locale)
(getenv "LANG")
(format-time-string "%Y-%m-%d %a")
(format-time-string "%A")
{{< /highlight >}}

termux android 端问题：插入的内容还是英文。
