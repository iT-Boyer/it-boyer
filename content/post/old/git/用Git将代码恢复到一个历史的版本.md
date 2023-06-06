---
title: 用Git将代码恢复到一个历史的版本
date: 2017-02-10 18:19:48
updated: 2018-09-05 15:52:33
comments: true
tags: [git]
categories:
- 解决方案
keywords: 
permalink: 
---

经历：将代码全提交到默认的head分支中，切换分支后，无法找到分支的严重后果：
目的：在当前分支上，将代码恢复到一个历史的提交版本上。

## 暴力的方式
如果仓库是自己在用（不影响别人），那么可以使用` git reset --hard <target_commit_id>` 来恢复到指定的提交，再用 git push -f 来强制更新远程的分支指针。为了保证万一需要找回历史提交，我们可以先打一个 tag 来备份。
1. 第一步：查看本地的索引的提交日志：
{% codeblock  lang:shell  %}
$ git reflog 		
a1d09fd HEAD@{0}: checkout: moving from all to master
a1d09fd HEAD@{1}: checkout: moving from master to all
a1d09fd HEAD@{2}: checkout: moving from HEAD to master
a1d09fd HEAD@{3}: checkout: moving from all to HEAD
{% endcodeblock %}
2. 第二步：根据上面的sh2值，回滚：
{% codeblock  lang:shell  %}
git reset  —hard  a1d09fd
{% endcodeblock %}
这样就可以找回代码.

## 温柔的方式
{% codeblock lang:shell %}
#回滚
git reset  —hard  a1d09fd
#将当前代码切换回最新的提交
git reset --soft origin/source
{% endcodeblock %}
此时工作区变成了历史的提交内容，这个时候用 `git add` 和 `git commit` 即可.

### 后悔药
- 舍弃上一次工作区的更改  
`git checkout .`
- 舍弃暂存区的更改  
`git reset --hard`
- 恢复上一次提交的内容到工作区  
`git reset -- .`  
`git reset filename`
- 先提交后再恢复到上一次提交的状态  
`git revert id`  
`git revert HEAD`
- 修改提交的日志分两步  
`git commit -am "日志信息"`  
`git commit -amend`

