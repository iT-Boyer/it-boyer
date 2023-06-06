---
title: Git如何永久删除文件(包括历史记录)
date: 2018-06-11 20:32:02
updated: 2018-06-11 20:32:02
comments: true
tags: [git]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
有些时候不小心上传了一些敏感文件(例如密码), 或者不想上传的文件(没及时或忘了加到.gitignore里的),

而且上传的文件又特别大的时候, 这将导致别人clone你的代码或下载zip包的时候也必须更新或下载这些无用的文件,

因此, 我们需要一个方法, 永久的删除这些文件(包括该文件的历史记录).

首先, 可以参考 [github帮助](https://help.github.com/articles/remove-sensitive-data)

## 使用bfg工具
[bfg官网](https://rtyley.github.io/bfg-repo-cleaner/) 
### 创建bfg别名
1. [下载bfg](https://search.maven.org/remote_content?g=com.madgag&a=bfg&v=LATEST) 到本地soft/bfg目录下。
2. 创建别名
sudo vi ~/.bash_profile  添加如下：

```
alias bfg="java -jar ~/Downloads/soft/bfg/bfg.jar"
```
### 常用命令
1. 克隆仓库
```
git clone --mirror git@github.com:OpenFibers/openfibers.github.com.git
```
2. 移除目标文件
然后就可以执行下面的任意一个或者多个操作。

从历史纪录中删除所有文件名是 `id_rsa` 或 `id_dsa` 的文件：
```
$ bfg --delete-files id_{dsa,rsa}  my-repo.git
```
从历史纪录中删除所有大于1M的二进制文件 :
```
$ bfg --strip-blobs-bigger-than 1M  my-repo.git
```
从文件中删除所有列出的密码：
```
$ bfg --replace-text passwords.txt  my-repo.git
```
删除目录及目录下文件:
`demo`:删除的目录
`--no-blob-protection`命令，可以解除保护
```
$ cd 
$ bfg --delete-folders demo --delete-files demo --no-blob-protection
//上述命令将demo索引状态重置为`add`的状态，需要执行reset即可
$ git reset HEAD demo
$ rm -fr demo
```

## 使用 filter-branch命令

## 步骤一: 从资料库中清除文件
```bash
$ git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch path-to-your-remove-file' --prune-empty --tag-name-filter cat -- --all
```
其中, `path-to-your-remove-file` 是要删除的文件的相对路径(相对于git仓库的跟目录), 替换成你要删除的文件即可.
>这里的文件或文件夹，都不能以 '/' 开头，否则文件或文件夹会被认为是从 git 的安装目录开始。

### 删除文件夹
在 `git rm --cached` 命令后面添加 `-r` 命令，表示递归的删除（子）文件夹和文件夹下的文件，类似于 `rm -rf` 命令:
```bash
$ git filter-branch --force --index-filter 'git rm --cached -r --ignore-unmatch path-to-your-remove-folder' --prune-empty --tag-name-filter cat -- --all
```
### 支持通配符
如果当要删除的文件很多, 文件或路径里有中文, 由于MinGW或CygWin对中文路径设置比较麻烦, 你可以使用通配符*号, 例如: `sound/music_*.mp3`, 这样就把`sound`目录下以`music_`开头的mp3文件都删除了.
使用通配符`*`删除目录下的所有文件：
```bash
$ git filter-branch --force --index-filter 'git rm --cached -r --ignore-unmatch path-to-your-remove-folder/*' --prune-empty --tag-name-filter cat -- --all
```
成功的日志：
```bash
Ref 'refs/heads/master' was rewritten
Ref 'refs/remotes/origin/master' was rewritten
WARNING: Ref 'refs/remotes/origin/master' is unchanged
WARNING: Ref 'refs/tags/v0.9.0' is unchanged
v0.9.0 -> v0.9.0 (2694a7834dada67cf8768ef27e2d7c3d777f5472 -> 2694a7834dada67cf8768ef27e2d7c3d777f5472)
```
>`Ref 'refs/heads/master' was rewritten`:表示成功；
`xxxxx unchanged`: 说明在当前分支里没有找到该文件.

## 步骤二: 推送我们修改后的repo
### 分支同步
通过步骤一，需要以强制覆盖的方式推送你的repo, 命令如下:
```bash
$ git push --force origin master
```
这个过程其实是重新上传我们的repo, 比较耗时, 虽然跟删掉重新建一个repo有些类似, 但是好处是保留了原有的更新记录, 所以还是有些不同的. 如果你实在不在意这些更新记录, 也可以删掉重建, 两者也差不太多, 也许后者还更直观些.
### tag同步
为了能从打了 tag 的版本中也删除你所指定的文件或文件夹，您可以使用这样的命令来强制推送您的 Git tags：
```bash
$ git push origin master --force --tags
```
## 步骤三: 清理和回收空间
虽然上面我们已经删除了文件, 但是我们的repo里面仍然保留了这些objects, 等待垃圾回收(GC), 所以我们要用命令彻底清除它, 并收回空间.

执行命令，再查看`.git`目录空间会明显变小:
```bash
$ rm -rf .git/refs/original/

$ git reflog expire --expire=now --all

$ git gc --prune=now


Counting objects: 2437, done.
# Delta compression using up to 4 threads.
# Compressing objects: 100% (1378/1378), done.
# Writing objects: 100% (2437/2437), done.
# Total 2437 (delta 1461), reused 1802 (delta 1048)


$ git gc --aggressive --prune=now


Counting objects: 2437, done.
# Delta compression using up to 4 threads.
# Compressing objects: 100% (2426/2426), done.
# Writing objects: 100% (2437/2437), done.
# Total 2437 (delta 1483), reused 0 (delta 0)
```
