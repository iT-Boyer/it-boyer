---
title: 迁移SVN库到git库保留branchs和tags
date: 2017-01-18 12:21:46
updated: 2019-01-16 21:01:13
comments: true
tags: [git]
categories:
- 解决方案
keywords: git,git-svn
permalink: 
---

SVN was a great advance in its day, but it’s now clear that distributed version control systems are the way forward and that Git is the de facto standard. Having helped many clients migrate from SVN to Git, here are my notes for a pain-free transition that will preserve the tags and branches in your SVN repository.

## 首先导入一个本地存储库

### 在本地创建一个存储库的目录
{% codeblock 新建目录 lang:bash http://www.sailmaker.co.uk/blog/2013/05/05/migrating-from-svn-to-git-preserving-branches-and-tags-3/#import-staging Create a local staging directory%}
cd ~
mkdir staging
cd staging
{% endcodeblock %}
>注：staging 可以用任何你喜欢的字符串命名，也可以放在本地的任何目录中。

### 初始化git svn
#### 使用SVN标准库结构初始化
{% codeblock 标准初始化 lang:bash %}
git svn init SVNRepo_ROOT_URL --stdlayout --prefix=svn/
{% endcodeblock %}
`SVNRepo_ROOT_URL`: 这里svn_url是完全限定的URL下的标准目录，其目录下包括三个目录：`trunk`，`branches`， `tags`。
`--prefix`: 强烈建议使用`svn/`作为分支和标签的前缀：设置为 `--prefix=svn/`. 这样有助于防止Git用户混淆原声的Git分支和标签。

#### 使用SVN自定义库结构初始化
使用非标准的svn layout 来新建svn库，即可以根据自己喜好来自定义分支，标签目录：
{% codeblock 非标准化 lang:bash %}
git svn init SVN_URL -T Trunk -b Branches -t Tags --prefix=svn/
{% endcodeblock %}

### 查看配置信息
1. `review`命令
{% codeblock lang:bash %}
review the config
{% endcodeblock %}
会有以下信息输出：
{% codeblock lang:bash %}
svn-remote.svn.url=svn://svn.example.com
svn-remote.svn.fetch=some/path/trunk:refs/remotes/svn/trunk
svn-remote.svn.tags=some/path/tags/*:refs/remotes/svn/tags/*
{% endcodeblock %}
高级用户可以在执行之前，修改相关配置。
2. `git config`命令
{% codeblock  lang:git %}
git config --local --list 

输出：
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
svn-remote.svn.url=https://huoshuguang@192.168.85.6/svn/PBBReader_Mac
svn-remote.svn.fetch=trunk:refs/remotes/origin/trunk
svn-remote.svn.branches=branches/*:refs/remotes/origin/*
svn-remote.svn.tags=tags/*:refs/remotes/origin/tags/*
remote.PBBReader.url=https://git.oschina.net/iTBoyer/PBBReader.git
remote.PBBReader.fetch=+refs/heads/*:refs/remotes/PBBReader/*
branch.master.remote=PBBReader
branch.master.merge=refs/heads/master
remote.server.url=https://server.local/git/PBBReader.git
remote.server.fetch=+refs/heads/*:refs/remotes/server/*
branch.v34.remote=PBBReader
branch.v34.merge=refs/heads/v28
{% endcodeblock %}

### 从远程SVN服务器拉取代码到新建的本地存储库
{% codeblock 拉取 lang:bash %}
git svn fetch
{% endcodeblock %}

## 查看本地库状态
### status
{% codeblock status lang:bash %}
git status
{% endcodeblock %}
输出：
{% codeblock 内容 lang:bash %}
# On branch master
nothing to commit (working directory clean)
{% endcodeblock %}

### 查看分支信息
{% codeblock branch lang:bash %}
git branch -a
{% endcodeblock %}
输出:
{% codeblock lang:bash  %}
* master
remotes/svn/tags/0.1.0
remotes/svn/trunk
{% endcodeblock %}
>注意：SVN标记和分支（在这种情况下，没有任何分支机构）仅作为远程引用存在。
    
## SVN分支和标签转为本地git仓库中的标签和分支
### SVN分支迁移
把远程svn分支转换为本地git仓库中的分支：
{% codeblock 分支转分支 lang:bash %}
for branch in `git branch -r | grep "branches/" | sed 's/ branches\///'`; do
git branch $branch refs/remotes/$branch
done
{% endcodeblock %}

### SVN标签迁移
1. 把远程svn标签转换为本地git仓库中的标签 :
{% codeblock tags转换tags lang:bash %}
for tag in `git branch -r | grep "tags/" | sed 's/ tags\///'`; do
git tag -a -m"Converting SVN tags" $tag refs/remotes/$tag
done
{% endcodeblock %}

### SVN标签转为本地git分支
2. 把远程svn标签转换为本地git仓库中的分支:
{% codeblock 标签转分支 lang:bash %}
for tag in `git branch -r | grep "tags/" | sed 's/ tags\///'`; do
git branch $tag refs/remotes/$tag
done
{% endcodeblock %}


## 在本地测试git命令push和clone操作

在推送到正式远程库之前，可以通过向本地git库中推送和clone操作。

### 创建一个临时的git库，用于测试push和clone测试
在git中的说法，`bare`库是一个不存在工作空间备份的库。
{% codeblock bare创建 lang:bash %}
cd ~
mkdir test
cd test
git init --bare
{% endcodeblock %}
这样，在`~/test`就生成了一个`bare`git库。
### push 测试
{% codeblock lang:bash %}
cd ~/staging
git remote add test `~/test`
git push --all test
git push --tags test
{% endcodeblock %}
把`~/test`的放在反引号中，反引号在命令行中会`~`自动补全为一个绝对路径。如果你给一个绝对路径或URL，可以省略反引号。
尽管它的名字，`--all`选项不推送`tags`，所以需要对标签单独push操作。
### clone 测试
{% codeblock lang:bash %}
cd ~
mkdir aclone
cd aclone
git clone ~/test
{% endcodeblock %}
There should now be a clone with a working copy in ~/aclone/test.
在`~/aclone/test`目录中将会clone出一个工作空间备份，检查确保一切OK，这样就可以向正式服务器上推送。

###  Push到正式git库中
如果你是正式库服务器（github，coding）的管理员，为本地git库设置一个空的git库。
以`Unfuddle`为例,路径如下：
`git@example.unfuddle.com:example/blah.git`
{% codeblock lang:bash %}
cd ~/staging
git remote add unfuddle REAL_HOST_URL
git push --all unfuddle
git push --tags unfuddle
{% endcodeblock %}
在上面的例子中，制定了远程名：`unfuddle`而不是默认的`origin`。当然，你可以使用任何你喜欢的名字。

## 清理操作
### 删除临时git库
{% codeblock lang:bash %}
cd ~/staging
git remote rm test
{% endcodeblock %}
`staging`库忽略`test`远程仓库.

### 清除clone生成的库
{% codeblock lang:bash %}
cd ~
rm -rf aclone
rm -rf test
{% endcodeblock %}

### Either keep or delete the staging repo
1. 如果需要Git和SVN之间频繁交互，建议保留`staging`库这会节省你非常耗时的初始化：
{% codeblock lang:bash %}
git svn fetch
{% endcodeblock %}
2. 如果你确信svn是报废的，你可以删除：
{% codeblock lang:bash %}
cd ~
rm -rf staging
{% endcodeblock %}


# 题外小贴士
在局域网内访问server搭建服务器提供的git服务：
{% codeblock 小贴士 lang:bash https://confluence.atlassian.com/fishkb/unable-to-clone-git-repository-due-to-self-signed-certificate-376838977.html SSL证书问题 %}
$ git clone https://.../git/mupdf.git
错误：fatal: unable to access 'https://..../git/mupdf.git/': SSL certificate problem: Invalid certificate chain
{% endcodeblock %}
解决：
{% codeblock  lang:bash %}
git config --global http.sslVerify false
{% endcodeblock %}










