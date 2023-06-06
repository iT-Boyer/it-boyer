---
title: 使用tag管理多个相关组件的版本发布
date: 2018-06-20 17:22:37
updated: 2018-06-21 21:43:37
comments: true
tags: [git]
categories:
- 解决方案
keywords: 
permalink: 
---
## tag标签自增新建脚本

```sh
#!/bin/sh
tag=$(git describe --tags `git rev-list --tags --max-count=1`)
version=${tag##*.}
let "version+=1"
newTag=${tag%.*}.${version}
echo 'Create New Tag '$newTag
```
## 修改tag版本号的方法
### 方法一：覆盖
1. 已有v1.0.2.8要覆盖该版本
```git
git tag -f v1.0.2.8
```
2. 服务器已有v1.0.2.8，强制推到服务器
```git
git push origin -f v1.0.2.8
```
3. 同步服务器：获取服务器刚刚的v1.0.2.8
```
git fetch -–tag
```
### 方法：删除分支
1. 删除本地版本
```git
git tag -d v1.0.2.8
```
2. 删除服务器上的分支(用空版本覆盖)
```ruby
git push origin :v1.0.2.8
```
3. 服务器获取刚刚的v1.0.2.8
```ruby
git fetch –-tag
```


