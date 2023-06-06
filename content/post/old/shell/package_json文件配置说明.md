---
title: package_json文件配置说明
date: 2018-10-14 23:47:05
updated: 2018-10-14 23:47:05
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
[npm 与 package.json 快速入门教程](https://blog.csdn.net/u011240877/article/details/76582670)
每个项目的根目录下一般都有一个package.json文件，定义项目所需要的各种模块，以及项目的配置信息。npm install则是根据这个配置文件，自动下载所需要的模块，也就是配置项目所需的运行和开发环境。 
package.json文件可以手工编写，也可以用npm init命令自动生成，除了项目名称和项目版本是必填的，其他都是选填的。

## 最简单的package.json
```
{
"name":"aaa",       //项目名称
"version":"0.0.0"   //项目版本(大版本.次要版本.小版本)
}
```
package是一个JSON对象，对象的每个成员就是当前项目的一项设置。

### script字段
script指定运行脚本命令的npm命令行缩写。
```
"script":{
    "start":"node index.js",
    "test":"tap test/*.js"
}
//运行npm run start时，执行node index.js命令
//运行npm run test时，执行tap test/*.js命令
```

### dependencies，devDependencies
dependencies和devDependencies两项，分别指定了项目运行所依赖的模块、项目开发所需要的模块。它们都指向一个对象，该对象的各个成员，分别由模块名和对应的版本要去组成，表示依赖的模块及其版本范围

--save参数表示将该模块写入dependencies属性，
--save-dev表示将该模块写入devDependencies属性。
```
{
    "devDependencies":{
        "browserify":"~13.0.0",
        "babel-core":"^6.5.0"       
    }
}
//模块名：对应的版本
```
### 对应的版本

指定版本: 比如1.2.2，安装时只安装指定版本1.2.2

波浪号(tilde) + 指定版本：比如~1.2.2，表示安装不低于1.2.2的1.2.x最新版本，但是不会安装1.3.x，等于只会影响小版本的版本号。

插入号(caret) + 指定版本 : 比如^1.2.2，表示安装不低于1.2.2的1.x.x最新版本，但是不会安装2.x.x，等于不会影响大版本号。如果大版本号为0，则插入号和波浪号效果一样，不会改变次要版本号。

latest:安装最新的版本

如果一个模块不在package.json文件汇总，则可以单独安装这个模块，并使用相应的参数将其写入package.json中。
```
$ npm install express --save
$ npm install express --save-dev
```
例如安装上面的express模块，–save表示将模块写入dependencies属性，–save-dev表示将模块写入devDependencies属性。

## 注释问题
package.json就是一个json文件，json本身只是一种数据格式，而不是程序语言，一般程序语言都会支持注释，但作为数据格式，它本身并不支持注释，所以只能通过其他方式绕过了。
