---
title: 当项目过大需要通过SSH方式Clone
date: 2018-04-04 17:44:24
updated: 2018-04-04 17:44:24
comments: true
tags: [git]
categories: 
- 解决方案
keywords: 
permalink: 
---
### 设置全局提交信息
```git
    git config --global user.email "you@example.com"
    git config --global user.name "Your Name"
```
### 修改最后一次提交的用户名信息
``git 
  git config user.name 'wangz'
  git config user.email 'wangz@alib.com'
  git commit  --amend --author=wangz  
```
### 项目过大问题
>git clone 主工程出现 fatal: The remote end hung up unexpectedly3)

通常的解决办法：
1. 设置提交缓存的大小为 1G：1048576000
    git config http.postBuffer 1048576000

2. 否则，需要配置github/gitlab的公钥
    生成：`ssh-keygen -t rsa -C "$your_email"`
    拷贝：`pbcopy < ~/.ssh/id_rsa.pub`
    创建SSHKey：
    在github/gitlab新建公钥`add SSH Key`：粘贴到密钥文本框中即可。
    


