---
title: Heroku部署web服务器
date: 2018-10-03 12:29:51
updated: 2018-10-18 18:39:22
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
[安装Heroku CLI工具](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)
Mac端
```
brew install heroku/brew/heroku
```
[Heroku CLI 命令](https://devcenter.heroku.com/articles/heroku-cli-commands)
```
heroku -h
heroku login
```
1. [Creating Apps from the CLI](https://devcenter.heroku.com/articles/creating-apps)
```
$ mkdir example
$ cd example
$ git init
$ heroku apps:create example
Creating ⬢ example... done
https://example.herokuapp.com/ | https://git.heroku.com/example.git
Git remote heroku added
```
2. [查看现有APP信息](https://devcenter.heroku.com/articles/heroku-cli-commands#heroku-apps-info)
```
heroku apps      //所有应用列表
heroku apps:info //查看所有应用的详细信息
```
3. [在浏览器中访问APP页面](https://devcenter.heroku.com/articles/heroku-cli-commands#heroku-apps-open-path)
```
USAGE
$ heroku apps:open [PATH]

OPTIONS
-a, --app=app        (required) app to run command against
-r, --remote=remote  git remote of app to use

EXAMPLES
$ heroku open -a myapp
# opens https://myapp.herokuapp.com

$ heroku open -a myapp /foo
# opens https://myapp.herokuapp.com/foo
```
## 用例：在heroku上部署`gh-oauth-server`服务
[issues:object ProgressEvent](https://github.com/imsun/gitment/issues/170)
下载服务器源代码[gh-oauth-server](https://github.com/imsun/gh-oauth-server) ,由于是`nodejs`写的所以需要安装`nodejs`环境 如何安装自己Google; 然后`git clone` 该项目并进入目录, 执行`npm install` 安装依赖, 依赖安装成功后执行`npm start`, 如果输出`start on port 300`表示开启成功,;为了支持`ssl` 可以安装`nginx代理`, 这个可以自己百度, 有很多教程的.
```
git clone https://github.com/imsun/gh-oauth-server
cd gh-oauth-server
heroku create gitment-hexo   //创建herokua应用
git push heroku master       //会自动安装package.json依赖库。
heroku open -a gitment-hexo  //浏览器打开
```
## 进入heroku服务器命令行
```
$heroku run bash
Running `bash` attached to terminal... up, run.3052
~ $ ls
Procfile  README.md  composer.json  composer.lock  vendor  views  web
~ $ exit
exit
```
### 参考
[Getting Started on Heroku with Node.js](https://devcenter.heroku.com/articles/getting-started-with-nodejs#prepare-the-app)
[把已经存在的应用部署到heroku上](https://devcenter.heroku.com/articles/preparing-a-codebase-for-heroku-deployment)learn how to prepare it for Heroku deployment.

