---
title: Hexo-hello-world
date: 2016-12-20 18:02:13
updated: 2018-09-13 19:22:55
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: hexo,发布
permalink: 
---
## 什么是 Hexo？
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

## 安装 Node.js
安装 `Node.js` 的最佳方式是使用 `nvm`,或者您也可以下载 [node.js安装包](http://nodejs.org/)来安装。

1. 安装`nvm`
方式一：cURL命令
```
$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh
```

方式二：linux方法Wget命令
```
$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
```
安装完成后，重启终端并执行下列命令即可安装 Node.js。

```
$ nvm install stable
```

2. 更新npm命令工具至最新版本
```
npm install -g npm
```

## 安装 Hexo
所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo。
```
$ npm install -g hexo-cli
```
## 建站
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
为了便于在多台电脑上部署博客，可以使用使用git版本库来管理个人博客的内容：   
具体部署过程：
```
git clone https://xxxxx/boyer.git boyer
cd boyer
npm install  #安装package.json是插件包，使用git管理更加便于管理安装的完整性。
```

新建完成后，指定文件夹的目录如下：
    .
    ├── _config.yml     # 网站的 配置 信息，您可以在此配置大部分的参数。
    ├── package.json    # 应用程序的信息。EJS, Stylus 和 Markdown renderer 已默认安装。
    ├── scaffolds       # 模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes
1. scaffolds   
模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。

2. source   
资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。
3. themes  
主题 文件夹。Hexo 会根据主题来生成静态页面。

### Create a new post

``` bash
$ hexo new "My New Post"
```

```bash
$ hexo new draft "草稿名" 
```

```bash
$ hexo publish "草稿名"  #Moves a draft post from _drafts to _posts folder.
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```
在我们开始之前,你必须在 `_config.yml` 修改设置。一个有效的部署设置必须有 `type` 字段。例如:
```bash
deploy:
    type: git
```
你可用同时部署到多个type，Hexo将依次执行每个部署。
```bash
deploy:
    - type: git
      repo:
    - type: heroku
      repo:
```
### 安装插件
#### 安装同步到git 插件
```bash
$ npm install hexo-deployer-git --save 
```
编辑`_config.yml`设置：
```bash
deploy:
    type: git
    repo: <repository url>
    branch: [branch]
    message: [message]
```
#### 安装生成RSS支持插件
```bash
$ npm install  hexo-generator-feed --save
```
编辑`_config.yml`设置：
```bash
feed:
    type: atom
    path: atom.xml
    limit: 20
    hub:
```

More info: [Deployment](https://hexo.io/docs/deployment.html)
