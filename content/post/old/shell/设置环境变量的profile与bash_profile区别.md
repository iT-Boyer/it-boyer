---
title: 设置环境变量的profile与bash_profile区别
date: 2018-10-05 09:32:44
updated: 2018-10-05 21:42:35
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## profile文件

1.1 profile文件的作用
**profile**(`/etc/profile`)，用于设置系统级的环境变量和启动程序，在这个文件下配置会对所有用户生效。当用户登录(login)时，文件会被执行。
1.2 在profile中添加环境变量
一般不建议在`/etc/profile`文件中添加环境变量，因为在这个文件中添加的设置会对所有用户起作用。
当必须添加时，我们可以按以下方式添加：
如，添加一个HOST值为linuxprobe.com的环境变量：
```
export HOST=linuxprobe.com
```
添加时，可以在行尾使用`;`号，也可以不使用。
一个变量名可以对应多个变量值，多个变量值需要使用`:`进行分隔。
添加环境变量后，需要重新登录才能生效，也可以使用`source`命令强制立即生效：
```
source /etc/profile
```
查看是否生效可以使用echo命令：
```
$ echo $HOST
linuxprobe.com
```
## bashrc文件
`bashrc`文件用于配置函数或别名。
`bashrc`文件有两种级别：系统级的位于`/etc/bashrc`、用户级的位于`~/.bashrc`，两者分别会对所有用户和当前用户生效。
`bashrc`文件只会对指定的`shell`类型起作用，`bashrc`只会被`bash shell`调用。
Mac OS X上的终端bash不读取`~/.bashrc`，因为Mac OS X上的bash是通过login的方式运行的，而`man bash`中写着，通过`login`方式登录的bash不会读取`~/.bashrc`。
解决方法：把下面的代码 添加到 `~/.bash_profile`中:
```
<!-- lang: shell -->
source ~/.bashrc
```

## bash_profile文件
`bash_profile`只对单一用户有效，文件存储位于`~/.bash_profile`，该文件是一个用户级的设置，可以理解为某一个用户目录下的`profile`。这个文件同样也可以用于配置环境变量和启动程序，但只针对单个用户有效。
