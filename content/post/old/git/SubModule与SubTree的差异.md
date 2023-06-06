---
title: SubModule与SubTree的差异
date: 2017-05-17 11:23:22
updated: 2018-10-11 20:58:56
comments: true
tags: [git]
categories:
- 学习笔记
keywords: 
permalink: 
---
## 核心区别 
`git submodule`类似于引用，而`git subtree`类似于拷贝，比如你在一篇博客中想用到你另一篇博客的内容，`git submodule`是使用那篇博客的链接，而`git subtree`则是将内容完全copy过来。

### submodule命令
1. 添加submodule
```
git submodule add -b branchA http://github.com/../repoA.git pathA
git submodule add http://github.com/../repoB.git pathB
```
执行以上命令后会生成`.gitmodule`的文件，文件存储子模块信息:
```sh
[submodule "repoA"]
    path = pathA
    url = http://github.com/../repoA.git
    branch = branchA
[submodule "ModuleB"]
    path = ModuleB
    url = http://github.com/../repoB.git
```
提交`.gitmodule`文件
```
git commit -m 'add submodule'
git push
```
2. clone新库初始化submodule
clone之后各模块内容为空目录，需要执行：
```
git submodule init
```
3.  拉取所有子模块
需要在`.gitmodules`文件中指定`branch:分支名`
```
 git submodule foreach git pull
 或
 git submodule foreach git pull origin master
```
4. 更新所有模块
更新后每个子模块并非在指定分支上，而是关联最近一次commitID。
使用`git submodule foreach`为每一个子模块执行切换命令:
```
git submodule foreach git checkout master
```
切换到所有master分支上（ModuleB为ResourceEvaluate，暂时需要单独执行）

5. 删除子模块
```
git rm --cached moduleA
rm -rf moduleA
vi .gitmodules //删除moduleA相关的内容
git add .
git commit -m "remove moduleA"
git push origin master
```

### subtree命令
1. `subtree`添加子模块
通过`subtree`添加子模块,`–squash`可省略，其功能是只有最新的提交记录被引入，去掉后则是引入所有历史提交记录
```
#增加远程仓库并设置引用名`ModuleA`，此步可省略，主要是为了简化后面的操作
git remote add ModuleA http://github.com/../ModuleA.git master
#`subtree`添加子模块
git subtree add --prefix=ModuleA --squash  ModuleA master
```
3. `subtree`更新
```
git subtree pull -P ModuleA ModuleA master
```
4. `subtree`提交
```
git subtree push --prefix=ModuleA ModuleA master
```

|/|submodule|subtree|结果|
|:-------:|:-------:|:--------:|:--------:|
|远程仓库空间占用 |submodule只是引用，基本不占用额外空间| 子模块copy，会占用较大的额外空间 |submodule占用空间较小，略优|
|本地空间占用 |可根据需要下载| 会下载整个项目 |所有模块基本都要下载，二者差异不大|
|仓库克隆|克降后所有子模块为空，需要注册及更新，同时更新后还需切换分支| 克隆之后即可使用 |submodule步骤略多，subtree占优|
|更新本地仓库 |更新后所有子模块后指向最后一次提交，更新后需要重新切回分支，所有子模块只需一条更新语句即可| 所有子模块需要单独更新 |各有优劣，相对subtree更好用一些|
|提交本地修改 |只需关心子模块即可，子模块的所有操作与普通git项目相同| 提交执行命令相对复杂一些 |submodule操作更简单，submodule占优|






