---
title: SwiftLint检查代码使用
date: 2018-08-21 17:02:47
updated: 2018-10-19 16:16:34
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
<!--github库卡片-->
{% github realm SwiftLint 8deb453 width = 30% %}

### 安装配置`swiftlint`语法矫正工具
####  安装包方式  
`brew install swiftlint` 或下载[swiftlint.pkg][https://github.com/realm/SwiftLint/releases/latest]
2. Xcode项目支持
在`Xcode build Phase`新增 "Run Script Phase":
```sh
if which swiftlint >/dev/null; then
swiftlint
else
echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
fi
```
#### 单个项目安装swiftlint
1. 通过cocoapods对单个项目安装swiftlint
```
pod 'SwiftLint'
```
2. 项目中添加支持  
```
"${PODS_ROOT}/SwiftLint/swiftlint"
```
> 注意：pod服务器上有时并不是最新版本，尝试`:=> githuburl`无用。这种情况智能使用安装包方式。

### 规则配置文件`.swiftlint.yml`

让 `SwiftLint` 在做代码规范检查时，不想检查某些源码，可以隔离规则，来自动忽略，例如： `CocoaPods`、`Carthage`、`SPM` 引入的第三方库文件。
在项目中新建 `.swiftlint.yml` 的配置文件：
```
$ cd your-project
$ vi .swiftlint.yml

excluded: 
    - Pods
```
`excluded`： 配置项用来设置忽略代码规范检查的路径，可以指定整个文件夹，也可以指定精确路径下的文件。
**支持嵌套**：
`.swiftlint.yml` 配置文件支持嵌套，可以给每个文件夹下的代码单独指定不同的规则，每个文件会匹配距离自己层级最近的父文件夹中的配置文件
嵌套的配置文件中的 `excluded` 和 `included` 配置会被忽略。

### 终端插件集成swiftLint
支持vim编辑器：
[keith/swift.vim](https://github.com/keith/swift.vim)

#### 安装 [syntastic](https://github.com/scrooloose/syntastic/)
使用[vim-pathogen](https://github.com/tpope/vim-pathogen.git)安装syntastic，
1. 第一步安装 `pathogen.vim`
```sh
mkdir -p ~/.vim/autoload ~/.vim/bundle && \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```
设置`~/.vimrc`配置项：
```sh
execute pathogen#infect()
```
2. 第二步将`syntastic` 作为 `Pathogen` bundle的资源方式安装
```sh
cd ~/.vim/bundle && \
git clone --depth=1 https://github.com/vim-syntastic/syntastic.git
```
#### 终端集成使用
在`vimrc`中添加配置，当启动vim即可使用：
```sh
let g:syntastic_swift_checkers = ['swiftpm', 'swiftlint']
```
1. 终端支持`Package.swift`
当存在`Package.swift`的swift目录中启动vim，swiftpm将自动可用。
2. 终端支持`.swiftlint.yml`
当存在`.swiftlint.yml`的swift目录中启动vim，且SwiftLint已安装，自动启用swiftlint。

