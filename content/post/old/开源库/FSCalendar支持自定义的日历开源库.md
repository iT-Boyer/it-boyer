---
title: FSCalendar支持自定义的日历开源库
date: 2018-07-17 17:59:05
updated: 2019-01-16 21:01:14
comments: true
tags: [SDK]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github it-boyer FSCalendar 1a026a4c width = 30% %}

<blockquote class="trello-card"><a href="https://trello.com/c/Tq2jnc1E/33-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%97%A5%E5%8E%86%E6%8E%A7%E4%BB%B6%EF%BC%8C%E6%97%A5%E6%9C%9F%E5%9B%BE%E6%A0%87">自定义日历控件，日期图标</a></blockquote><script src="https://p.trellocdn.com/embed.min.js"></script>

[FSCalendar](https://github.com/it-boyer/FSCalendar)
FSCalendar是一款开源iOS日历控件，支持横向、纵向滑动模式，全屏模式，带有子标题、事件设置等功能。以下是项目截图：
![](https://cloud.githubusercontent.com/assets/5186464/10262249/4fabae40-69f2-11e5-97ab-afbacd0a3da2.jpg)
Use Interface Builder
1、 Drag an UIView object to ViewController Scene 2、 Change the Custom Class to FSCalendar
3、 Link dataSource and delegate to the ViewController 
4、 Finally, implement FSCalendarDataSource and FSCalendarDelegate in your ViewController
![](https://cloud.githubusercontent.com/assets/5186464/9488580/a360297e-4c0d-11e5-8548-ee9274e7c4af.jpg)
### 预览效果

