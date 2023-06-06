---
title: subtree拆分大的git库到多个独立git库
date: 2018-10-09 12:07:55
comments: true
tags: [git]
keywords: 
categories:
- 学习笔记
---
[Git 仓库拆拆拆](https://segmentfault.com/a/1190000002548731)
## 拆分一个子目录为独立仓库
```sh
# 这就是那个大仓库 big-project
$ git clone git@github.com:tom/big-project.git
$ cd big-project

# 把所有 `codes-eiyo` 目录下的相关提交整理为一个新的分支 eiyo
$ git subtree split -P codes-eiyo -b eiyo

# 另建一个新目录并初始化为 git 仓库
$ mkdir ../eiyo
$ cd ../eiyo
$ git init

# 拉取旧仓库的 eiyo 分支到当前的 master 分支
$ git pull ../big-project eiyo
```
## 清除一个子目录下所有内容和记录
这个还是要用万能的 `filter-branch`：
```sh
# 还是那个大仓库 big-project
$ git clone git@github.com:tom/big-project.git
$ cd big-project

# 清理 `master` 分支上所有跟 `codes-eiyo` 目录有关的痕迹
$ git filter-branch --index-filter "git rm -rf --cached --ignore-unmatch codes-eiyo" --prune-empty master

# 另建一个新目录并初始化为 git 仓库
$ mkdir ../big-project-fresh
$ cd ../big-project-fresh
$ git init

# 拉取 `big-project` 的 `master` 分支（到新仓库的 master 分支）
$ git pull ../big-project master
```
## 推送给新的远端仓库
```sh
$ git remote add origin git://github.com:tom/fresh-project.git
$ git push origin -u master
```
### git subtree 合并
git subtree：合并策略，通过git subtree命令能将依赖库某分支合并到主项目的分支中，在开发过程中，只需对主项目分支进行统一管理。

#### 创建合并subtree add命令
命令如下:
```
'git subtree' add -P <prefix> <commit>
'git subtree' add -P <prefix> <repository> <ref>
```
##### 两步走
先fetch库再subtree add合并
1. 先将依赖库fetch到本地仓库中
```sh
// 创建lib的远程版本库: 
git init --bare lib-rep
#-f：远端库添加后立即执行fetch操作
git remote add -f librepo ../lib-rep
```
2. 通过git subtree命令将依赖库合并到主项目中
```
git subtree add -P lib librepo master  
```

##### squash合并法
`--squash`: 适用于add/pull/merge子命令。先合并引用库的更新记录，将合并结果并到主项目中。
```sh
git subtree add --prefix=themes/jacman --squash https://it-boyer@github.com/it-boyer/jacman.git master   
```
#### subtree其他命令
git subtree支持创建(add)之外,还支持更新(pull),推送(push),合并(merge),抽离(split)以下命令:
```bash
'git subtree' pull -P <prefix> <repository> <ref>
'git subtree' push -P <prefix> <repository> <ref>
'git subtree' merge -P <prefix> <commit>
'git subtree' split -P <prefix> [OPTIONS] [<commit>]
```
从子树库中拉取最新代码:
```
git subtree pull --prefix=themes/jacman --squash https://it-boyer@github.com/it-boyer/jacman.git master
```
将自己的代码发布到子树库:
```
git subtree push --prefix=themes/jacman --squash https://it-boyer@github.com/it-boyer/jacman.git master
```
相关参数：
```bash
-q | --quiet
-d | --debug
-P <prefix> | --prefix=<prefix>      引用库对应的本地目录
-m <message> | --message=<message>   适用于add/pull/merge子命令。设置产生的合并提交的说明文本
--squash                             适用于add/pull/merge子命令。先合并引用库的更新记录，将合并结果并到主项目中。
使用此选项时，subtree add/pull会产生两个提交版本：一个是子项目的历史记录，一个是Merge操作。好处是可以让主项目历史记录很规整，缺点是子项目更新时常常需要解决冲突。一个更好的解决方案是：单独建一个分支进行--no-squash的subtree更新，然后再--squash合并到主分支。每次在此分支做操作前都需要先把主分支合并进来。参考：http://www.fwolf.com/blog/post/246
```

split子命令选项：
```
--annotate=<annotation>              创建合成历史时有可能形成内容不同但提交信息完全相同的提交版本，使用这个选项在每个提交消息前加上此前缀用来区分。
-b <branch> | --branch=<branch>      创建合成的提交历史时，创建此参数指定的新分支包含生成的合成历史。<branch>必须是还不存在的。
--onto=<onto>
--rejoin
--ignore-joins

```

#### 使用sourcetree管理

1. 配置 subtree
菜单：Repository -> Add/Link subtree...(添加／链接子树...)
在左边栏的SUBTREES(子树)中显示：
2. 拉取依赖库的最新代码
在右边栏右击已存在的subtree，并选择 pull subtree...菜单项：
第二步的功能代码如下:
```
git -c subtree pull -P themes/.jacman --squash https://it-boyer@github.com/it-boyer/jacman.git master 

```
