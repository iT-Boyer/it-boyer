---
title: 使用curl命令管理远程github库
date: 2018-06-20 11:14:00
updated: 2018-09-05 15:52:33
comments: true
tags: [github]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
使用`curl`命令管理远程github库
## 新建远程仓库
1. 在本地准备工作
进入一个目录，这个目录是本地仓库的目录；
在本地建立仓库
```sh
git init && git add . && git commit -m 'some information'
```
2. 新建一个`API token`
打开此链接，[generate new token](https://github.com/settings/tokens)，写入`description`，选择`scopes`（设置此token持有者的权限）。记住`personal access token`（也就是那一串字符和数字）！这一串东西只出现一次，下次查看不到。

### 基础命令
这是最直接的一种形式，直接把参数写到命令行搞定：
```git
curl -u git账号 -d '{"name":"新库名","description":"库描述"}' https://api.github.com/user/repos
curl -u "$username:$token" https://api.github.com/user/repos -d '{"name":"'$repo_name'"}'
```
>注：这里需要把`$username`和`$token`分别换成实际的用户名和刚才记住的`personal access token`，把`$repo_name`换成任何想要的`repo name`。
### bash 形式
我们可以把命令行写成bash脚本，下次只要执行里面的简单命令就可以执行以上整条命令。
1. 把`username`和`token`写入(apend或者修改)`~/.gitconfig`，形式如下：
```
[github]
    user = your user name 
    token = the token you get
```
2. 把如下 `bash code`写入（append）`~/.bash_profile`文件
```bash
github-create() {
    repo_name=$1

    dir_name=`basename $(pwd)`

    if [ "$repo_name" = "" ]; then
    echo "Repo name (hit enter to use '$dir_name')?"
        read repo_name
    fi

    if [ "$repo_name" = "" ]; then
        repo_name=$dir_name
    fi

    username=`git config github.user`
    if [ "$username" = "" ]; then
        echo "Could not find username, run 'git config --global github.user <username>'"
        invalid_credentials=1
    fi

    token=`git config github.token`
    if [ "$token" = "" ]; then
        echo "Could not find token, run 'git config --global github.token <token>'"
        invalid_credentials=1
    fi

    if [ "$invalid_credentials" == "1" ]; then
        return 1
    fi

    echo -n "Creating Github repository '$repo_name' ..."
    curl -u "$username:$token" https://api.github.com/user/repos -d '{"name":"'$repo_name'"}' > /dev/null 2>&1
    echo " done."

    echo -n "Pushing local code to remote ..."
    git remote add origin git@github.com:$username/$repo_name.git > /dev/null 2>&1
    git push -u origin master > /dev/null 2>&1
    echo " done."
}
```
* 也可以将脚本保存在`github-create.sh`文件中，让后在`~/.bash_profile`添加导入语句
```
. "$HOME/your path/github-create.sh"
```
* 也可以导入到`oh-my-zsh`配置文件`zshrc.zsh-template`中，每次创建myzsh窗口时，`github-create`方法会初始化在环境中：
```
. "$HOME/hsg/hexo/Util/tool/github-create.sh"
```
3. 重新打开或新启动一个终端窗口，或者也可以在当前`Terminal`下运行如下命令
```
Source  ~/.bash_profile
```
4. 然后就可以用如下命令创建远程仓库了
```
github-create [repo name]
```
如果你不想用默认`repo name`（也就是当前目录名）创建repo可以重新输入另一个名字，否则直接按回车执行。
### bash形式--简化版
1. 把如下`bash code`写入（append）`~/.bash_profile`文件。第十行按照形式一处理一下。
```
simple-create() {
    if [ $1 ]
    then
        repo_name=$1
    else
        echo "Repo name?"
        read repo_name
    fi
    
    curl -u '$username:$token' https://api.github.com/user/repos -d '{"name":"'$repo_name'"}'
    git remote add origin git@github.com:efatsi/$repo_name.git
    git push -u origin master
}
```
2. 重新打开或新启动一个终端窗口，或者也可以在当前`Terminal`下运行如下命令
```
Source  ~/.bash_profile
```
3. 执行命令
```
simple-create [repo name]
```

## 查询现有库
```
curl -u git账号 https://api.github.com/repos/账号/库名
```

## 初始化远程仓库
找到仓库路径的字段`clone_url`或者
```git
git remote add origin clone_url
//或者使用ssh,避免输入密码
//git remote add origin ssh_url
git push origin master
```

## 删除远程仓库
```ruby
githubDelRepo(){
    if [[ $# != 2 ]] ; then
        echo "Needs username and repo-name as args 1 and 2 respectively."
    else
        curl -X DELETE -u "${1}" https://api.github.com/repos/"${1}"/"${2}"
    fi
}
```



