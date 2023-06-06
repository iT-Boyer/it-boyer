---
title: Hexo开发文档及使用教程
date: 2018-09-07 15:08:22
updated: 2018-09-07 15:08:22
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github hexojs hexo  0b26940 width = 30% %}

[](https://hexo.io/zh-cn/docs/contributing)

### 开始之前
请遵守以下准则：

* 遵守 Google JavaScript 代码风格。
* 使用 2 个空格缩排。
* 不要把逗号放在最前面。
### 工作流程

1. Fork [hexojs/hexo](https://github.com/hexojs/hexo)
2. 把库（repository）复制到电脑上，并安装所依赖的插件:
```
$ git clone https://github.com/<username>/hexo.git
$ cd hexo
$ npm install
$ git submodule update --init
```
3. 新增一个功能分支:
```
$ git checkout -b new_feature
```
4. 开始开发。
5. 推送（push）分支:
```
$ git push origin new_feature
```
6. 建立一个新的合并申请（pull request）并描述变动.
### 注意事项
不要修改 `package.json` 的版本号。
只有在测试通过的情况下您的合并申请才会被批准，在提交前别忘了进行测试。
```
$ npm test
```

