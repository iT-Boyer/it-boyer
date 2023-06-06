---
title: GitBook编辑器使用
date: 2018-08-22 19:51:19
updated: 2019-01-16 21:01:13
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
{% github GitbookIO gitbook 6efbb70 width = 30% %}

[GitBook Editor](https://legacy.gitbook.com/editor)
[个人中心](https://legacy.gitbook.com/@iTBoyer/dashboard)
### 制作书籍
1. 在github找到文档库，fork为自己的库
2. 登录GitBook`个人中心`，点击`+ New`新建book
3. 在建库页面，选择导入`git hub`现有库
    Import and sync an existing GitHub repository.
4. 进入`Personal settings`，进入github设置面板
5. 点击`Configure`按钮，进入github授权gitbook页面
GitBook does not have the permissions to access every GitHub repositories linked to your books. Please add them to your configuration to continue editing your  books.
6. github中设置 `Repository access`
1. All repositories
    This applies to all current and future repositories.
2. Only select repositories

### gitbook终端命令行
[命令行文档](https://toolchain.gitbook.com/ebook.html)
[GitBook 制作 Kindle 电子书详细教程（命令行版）](https://bookfere.com/post/288.html)
1. 安装
```
$ npm install gitbook-cli -g
```
2. 创建电子书项目
新建一个目录，并进入该目录使用 gitbook 命令初始化电子书项目。举个例子，现在要创建一个名为“MyFirstBook”的空白电子书项目，如下所示：
```
$ mkdir MyFirstBook
$ cd MyFirstBook
$ gitbook init
```
3. 预览电子书内容
电子书内容编写完毕后可以使用浏览器预览一下。先输入下面的命令据 .md 文件生成 HTML 文档：
```
$ gitbook build
```
> Error: Couldn't locate plugins "jsbin, anchors, video, ga, toggle-chapters, editlink, include-codeblock, splitter, github-buttons, chart, todo, quiz, include-highlight, tonic", Run 'gitbook install' to install plugins from registry.
执行安装插件命令：
```
gitbook install
```
生成完毕后，会在电子书项目目录中出现一个名为“_book”的文件夹。进入该文件夹，直接用浏览器打开“index.html”，或先输入下面的命令：
```
$ gitbook serve
```
然后在浏览器中输入`“http://localhost:4000”`即可预览电子书内容，预览完毕后按 Ctrl + C 结束。

4. 生成电子书文件
确定电子书没有问题后，可以通过输入以下命令生成 mobi 电子书：
```
$ gitbook mobi ./ ./MyFirstBook.mobi
```
>error: error while generating page "introduction.md": InstallRequiredError:"svgexport" is not installed. Install it using: "npm install svgexport -g"
执行安装命令：
```
npm install svgexport -g
```
如果出现以下错误提示，说明您还未安装 Calibre。由于 GitBook 生成 mobi 格式电子书依赖 Calibre 的 ebook-convert，所以请先[点击这里](https://bookfere.com/tools#calibre)下载安装 Calibre。
```
Error: Need to install ebook-convert from Calibre
//或者使用命令安装
brew install homebrew/cask/calibre 
```
Calibre 安装完毕后，对于 Mac OS X 系统，还需要先设置一下软链接：
```
$ ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin
```
再次运行转换命令，即可生成 mobi 格式电子书。

