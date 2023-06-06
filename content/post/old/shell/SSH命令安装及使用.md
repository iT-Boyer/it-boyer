---
title: SSH命令安装及使用
date: 2019-02-26 18:16:23
updated: 2019-02-26 18:29:38
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---

写下这篇文章的目的是为了更清楚的讲解和展现ssh的用法和操作，避免网上庞杂的文章和材料影响新手的学习过程。所以如果你是为老手，可以略过这篇文章了。


<!-- TOC GFM -->

* [SSH介绍](#ssh介绍)
* [安装使用](#安装使用)
    * [OpenSSH的安装](#openssh的安装)
    * [使用](#使用)
        * [配置和启动ssh服务](#配置和启动ssh服务)
        * [登录到远程主机](#登录到远程主机)
        * [配置使用key登录ssh服务器ssh免密码登录](#配置使用key登录ssh服务器ssh免密码登录)
        * [总结](#总结)
* [本章总结](#本章总结)
* [更多参考内容](#更多参考内容)

<!-- /TOC -->

## SSH介绍
1. 什么是ssh？
ssh是“Secure Shell”的简写，Secure Shell协议是国际互联网工程任务组（The Internet Engineering Task Force，简称 IETF）制定的一个标准。目的是为了创建一个工作在应用层和传输层基础上的安全协议。避免数据的的明文传输。
2. 什么是OpenSSH？
前边说了，ssh是网络协议，而OpenSSH就是其中的一个具体实现。OpenSSH是由OpenBSD管理的项目之一，不过Openssh是跨平台的，支持linux、unix*，甚至windows。
所以实际应用中，我们用到的ssh基本上都是Openssh。
3. OpenSSH有哪些功能？
Secure Shell 是一个通信协议，在这个协议之上可以实现很多种应用层协议。从OpenSSH官网来看，OpenSSH提供了以下几个应用：
    1、ssh，登录远程服务器、在远程服务器上执行命令。
    2、scp，在两台主机之间实现文件拷贝。
    3、sftp，基于openssh实现的类似ftp程序。

除此之外，OpenSSH还提供了几个命令行工具，用来方便进行ssh操作：
1、ssh-add
2、ssh-keysign
3、ssh-keyscan
4、ssh-keygen

## 安装使用
下文将讲述这些命令的实际用法。
### OpenSSH的安装
大部分linux发行版都默认包含了OpenSSH客户端和服务器端，一些linux桌面发行版没有安装openssh服务器端。
如果没有安装，我们可以通过linux发行版的软件包管理工具进行安装。简单来说就是：
apt-get系列：
```sh
sudo apt-get install openssh openssh-server
```
yum系列：
```sh
sudo yum install openssh openssh-server
```
其他系列：自己百度下。
备注：openssh是客户端、openssh-server是服务器端。不同的发行版名称可能不同，需要自己确认一下。
安装完毕之后，系统中就应该会有ssh命令了。这个时候就可以使用ssh来进行远程主机的管理了。

### 使用
####  配置和启动ssh服务
在使用之前我们需要对ssh服务进行配置，在大多数linux系统中，ssh服务的配置文件为：/etc/ssh/sshd_config
使用vim进行编辑：
```sh
vim  /etc/ssh/sshd_config
```
以下地方根据实际情况进行修改（yes为允许、no为禁止）：
```
PermitRootLogin yes  #是否允许root账户登录
PasswordAuthentication yes  #是否允许使用密码校验登录
RSAAuthentication yes  
PubkeyAuthentication yes  #是否允许使用key登录
```

然后使用系统服务管理命令启动服务，在大部分linux系统下，命令为：service 或者 systemctl
启动ssh服务命令：
```
~# service sshd restart
```
或者
```
~# systemctl  restart sshd
```

#### 登录到远程主机
命令格式为：ssh  用户名@远程主机的ip地址:远程主机端口
示例：
```sh
ssh  root@192.168.0.1:22   
```
或者：
```sh
ssh -p 22 root@192.168.0.1
```

> 注：ssh默认端口为22，远程主机为默认端口时，可省略端口号。

执行上述命令之后，首次登陆会询问是否保存秘钥，输入yes即可。然后会提示输入密码，输入该用户对应的远程主机密码即可。

#### 配置使用key登录ssh服务器ssh免密码登录
使用key登录，需三个步骤：
1. 修改ssh服务配置文件允许key登录：
```
~# vim /etc/ssh/sshd_config
```
找到PubkeyAuthentication这一行，改成：
```
PubkeyAuthentication  yes
```
2. 重启ssh服务：
```
~# service sshd restart
```
或者
```
~# systemctl  restart sshd
```

3. 使用ssh-keygen命令在本地机器上生成Rsa公钥对：
```
~# ssh-keygen -t rsa
```
执行上述命令后，会提示输入要保存的文件路径，默认为：~/.ssh/id_rsa.

输入文件名，点回车，会提示输入秘钥的密码（会提示输入两遍），即可生成秘钥文件：


查看秘钥文件

4. 将公钥文件上传到服务上：
```
~# ssh-copy-id -i /home/zhao/.ssh/id_rsa_leilei.pub
root@192.168.0.1
```
`-i` 是用来指定公钥文件，执行命令之后，按提示输入远程密码即可。
5. 然后即可使用私钥文件登录服务器：
```
~# ssh -i /home/zhao/.ssh/id_rsa_leilei   root@192.168.0.1
```
> 注意：如果在第3步时为秘钥设置了密码，则使用秘钥登录服务器时，需要输入秘钥密码。

#### 总结
1. 如果想实现免密登录，则只需要在第三步生成密钥对时不要设置秘钥密码。
2. 如果使用秘钥文件使用默认文件名（id_rsa），则在使用ssh的过程中就不需要再使用-i开关来指定秘钥文件了。

4. 拷贝文件到远程主机：
ssh中提供了scp命令用来拷贝文件到远程主机，使用方式为：
```
~# scp -i /home/zhao/.ssh/id_rsa_miyao  a.tar.gz root@192.168.0.1:/home/zhao/
```
就能将文件 `a.tar.gz` 拷贝到远程主机`/home/zhao/`下  
5. 在远程服务器上执行命令：
直接将需要执行的命令追加到ssh命令后面即可，如：
```
~# ssh -i /home/zhao/.ssh/id_rsa_miyao root@192.168.0.1  "ls -l"
```
即可在远程服务器上执行“`ls -l`” 命令，结果将输出到本地控制台。
但是在执行一些命令时，远程主机会提示无法找到该命令，这说明需要设置远程主机的环境变量，可在发送给远程主机的命令中增加source指令，如：
```
~# ssh -i /home/zhao/.ssh/id_rsa_miyao  root@192.168.0.1 "source ~/.bash_profile && ls -l"
```

## 本章总结
1. 本文的命令示例中，均使用了 `-i` 开关来指定秘钥文件，如果使用默认秘钥文件：`~/.ssh/id_rsa `登录，则均可省略-i开关。
2. 其他用法后续再补充吧。
## 更多参考内容
https://man.openbsd.org/ssh-add.1
https://man.openbsd.org/?query=ssh-keygen&sektion=1
