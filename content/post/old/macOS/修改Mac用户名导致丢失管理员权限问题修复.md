---
title: 修改Mac用户名导致丢失管理员权限问题修复
date: 2018-09-25 19:16:53
updated: 2018-09-25 19:41:17
comments: true
tags: [macOS]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## 出现的问题主要有两点

1. 数据恢复问题
1）修改用户名后，mac系统认为是创建了一个新的用户，这时候，之前管理员账号的数据在当前用户的finder里是看不到的。这时候可以去到桌面，点击又上角的“前往” 。
2. 管理员权限问题
虽然数据问题恢复了，但是另外一个更严重的问题又出现了。新的用户名账号只是一个普通用户账号，没有管理员权限没有管理员权限了。在需要管理员权限的操作中输入具有管理员权限的前用户名和密码也不认了。
解决办法：
1. 创建一个新的具有管理员权限的用户
第一步：重启电脑，并长按组合键：`command+s`进入Single User Model模式，出现像DOS一样的提示符 。
第二步：在命令行顺序输入命令： 
``` 
fsck -y
mount -uaw /
rm /var/db/.AppleSetupDone
reboot
```
苹果电脑会重启，并且在开机后出现新装机时的欢迎界面。重新建立一个新的管理员账号。 
