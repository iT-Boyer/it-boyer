+++
title = "借助环境变量简化shell脚本"
date = 2019-08-09T15:22:00+08:00
lastmod = 2021-05-08T11:52:49+08:00
tags = ["shell"]
draft = false
author = "iTBoyer"
+++

## 需求描述 {#需求描述}

本人使用oh-my-zsh终端主题，它提供了很多快捷命令，还有平时也积累的一些alias快捷命令,配置在.zshrc中，方便每次打开终端，加载命令工具。但是如何在shell脚本中利用之前积累的命令，来简化脚本的编写呢。


## 场景 {#场景}

我使用hugo静态网页框架搭建了个人博客，部署到github-Page。博客中的图片是占用了大部分宝贵的空间，为了瘦身，编写一个一键部署的脚本deploy.sh，旨在自动编译，图片压缩，然后再发布到托管服务器上。


## 实践 {#实践}

1.  压缩图片压缩图片是工作中常有的需求，之前已经有封装好的快捷命令：
    [Python获取当前文件路径 - 简书](https://www.jianshu.com/p/bfa29141437e)

    ```shell
      ~/.../pngquant/compresspng.py  imgDir #默认目录路径
    ```

    为了简化命令，借助alias别名工具，并在.zshrc 加载:

    ```js
        alias pngTo="$hsgToolDir/pngquant/compresspng.py "
    ```

    重新打开终端，就可以找pngTo命令

    ```sh
      where pngTo
      pngTo imgDir #压缩imgDir目录下的图片
    ```
2.  在deploy.sh使用pngTo命令开始想当然的直接在deploy.sh中直接调用：

    ```sh
       #压缩图片
       pngTo `pwd`/static/ox-hugo
    ```

    执行过程提示:找不到 pngTo 命令.

    原因是，想使用环境，还必须需要source名加载环境文件.zshrc.

    ```sh
       #压缩图片
       source ~/.zshrc
       pngTo `pwd`/static/ox-hugo
    ```


## 完整脚本 {#完整脚本}

```shell
#!/bin/zsh

#  deploy.sh
#  hugo
#
#  Created by admin on 2019/8/8.
#
echo -e "Deploying updates to Github..."

source ~/.zshrc
# 压缩图片
where pngTo
pngTo `pwd`/static/ox-hugo
pngTo `pwd`/../iPics/images

# build the project
hugo -t even

deploy()
{
    git remote -v

    git add .

    msg="rebuilding site `date`"

    if [ $# -eq 1 ]
    then msg="$1"
    fi

    git commit -m "$msg"

    # push source to github

    git push origin HEAD:master

    # come back to blog root
    cd ..
}


cd public
deploy

###
echo "输入commit msg："
read msg
deploy $msg

```
