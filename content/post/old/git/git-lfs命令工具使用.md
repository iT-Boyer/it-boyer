---
title: git-lfs命令工具使用
date: 2018-10-11 17:58:15
updated: 2018-10-11 17:58:15
comments: true
tags: [git]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github git-lfs git-lfs 45c4568 width = 30% %}
[git-lfs官网](https://git-lfs.github.com)
LFS其实是git的一个扩展，并没有改变git的工作方式，有点像耍了个小花招，把指定需要lfs管理的文件替换成了一个指针文件交给git进行版本管理；
在pull/push等这些操作中，lfs又通过lfs服务器把这些文件的真身给下载或上传回来；
通过这样的手段，使得本地仓库的体积大大减小，而不会出现随着这些文件的版本增多而体积剧烈膨胀的情况；
个人觉得这种把存储负担转移给了服务器的做法，是不是有违git去中心化的理念，毕竟lfs这样做其实算是强依赖于这个lfs服务器了，本地仓库并不是一个完整的仓库
### [安装](http://macappstore.org/git-lfs/)
```
brew install git-lfs
```
### 配置git库支持git-lfs
1. Git命令行扩展工具`git-lfs`,您只需设置一次`Git LFS`。
```
#设置库支持
git lfs install
#删除库支持
git lfs uninstall
```
> 当使用gitee时，push远程库：WARNING: Authentication error: Authentication required: not a enterprise project 
2. 配置`.gitattribute`文件，添加需要`Git LFS`管理的文件类型。也可`track`命令添加其他类型
```
# 添加
git lfs track "*.psd"
# 查看规则
git lfs track
# 查看跟踪的文件清单
git lfs ls-files
```
3. 确保跟踪`.gitattributes`
```
git add .gitattributes
```
4. 像往常一样提交并推送到GitHub。
```
git add file.psd
git commit -m "Add design file"
git push origin master
```
