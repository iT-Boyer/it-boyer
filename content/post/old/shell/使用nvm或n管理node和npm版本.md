---
title: 使用nvm或n管理node和npm版本
date: 2018-05-29 12:06:24
updated: 2018-10-22 20:02:16
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
---
问题
```
Fatal error in , line 0
# Check failed: !value_obj->IsJSReceiver() || value_obj->IsTemplateInfo().
#FailureMessage Object: 0x7ffeefbf25c0[1]    22749 illegal hardware instruction  he
```
参考[Fatal error in ../deps/v8/src/api.cc, line 1197 when gulp watch](https://stackoverflow.com/questions/45623774/fatal-error-in-deps-v8-src-api-cc-line-1197-when-gulp-watch),需要降级node版本。
## nvm工具
nvm是node版本管理工具，参考[官网nvm安装指南](https://github.com/creationix/nvm#installation)
1. 安装
```sh
$ brew install nvm
```
2. 安装node
```
$ nvm install versionnum  //安装
$ nvm use versionnum      //使用指定版本
$ nvm ls                //查看本地node
->  v6.14.4
    v8.12.0
    system
    default -> 8 (-> v8.12.0)
```
3. 使用npm
`nvm`安装`node`之后，会安装对应版本`npm`工具，如：node 8 对应 npm v5，node 7 对应 npm v4
```
npm -v //查看当前node对应的npm版本号
npm version //查看当前目录使用的node详情，即node_modules安装时使用node版本号
```
>注意：在hexo执行，npm install 之后，hexo g 失败时，需要降级npm版本，这是无论怎么使用nvm use 来设置当前node，都无效，必须删除node_modules目录，再使用nvm use切换低版本，重新安装package.json插件才行。

## 工具包升级node
[issue-431242848](https://github.com/everblogjs/everblog/issues/10#issuecomment-431242848)
node有一个专门管理node.js的版本工具模块n
首先安装n模块：`npm install -g n` 
升级node.js到最新稳定版
```
n stable
```

