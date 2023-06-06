---
title: Docker使用
date: 2016-12-21 21:25:29
updated: 2019-01-16 21:01:14
tags: [docker]
categories:
- 学习笔记
---

## 安装[Docker for Mac](https://www.docker.com/products/docker#/mac) 
在Mac上运行Docker。系统要求，OS X 10.10.3 或者更高版本，至少4G内存，4.3.30版本以前的VirtualBox会与Docker for Mac产生冲突，所以请卸载旧版本的VitrualBox。
```ruby
echo '下载dmg...'
curl -o Docker.dmg https://dn-dao-github-mirror.qbox.me/docker/install/mac/Docker.dmg
#安装Docker.dmg
MOUNTDIR=$(echo `hdiutil mount Docker.dmg | tail -1 \
| awk '{$1=$2=""; print $0}'` | xargs -0 echo) \
&& cd ${MOUNTDIR} && cp -R Docker.app /Applications/ \
&& open /Applications/Docker.app
```
### 配置 Docker 加速器镜像源
[国内加速器](https://www.daocloud.io/mirror)
右键点击桌面顶栏的 docker 图标，选择 Preferences ，在 Advanced 标签下的 Registry mirrors 列表中加入下面的镜像地址:
```
http://f1361db2.m.daocloud.io
```

点击 Apply & Restart 按钮使设置生效。

### 安装主机监控程序加速器
---
1. 登录到 `DaoCloud 控制台`，点击「我的集群」按钮，在「接入自有主机」界面，点击 Mac 按钮。
2. 安装[DockerToolbox](https://github.com/docker/toolbox/releases/)，是一个完整的开发组件，通过安装和配置`DaoCloud加速器 v2`，提升下载 `Docker Hub 镜像`的速度。
```
//下载pkg
curl -o DockerToolbox.pkg https://github.com/docker/toolbox/releases/download/v18.09.0/DockerToolbox-18.09.0.pkg
//安装pkg
sudo installer -pkg DockerToolbox.pkg -target /
```
<!--More-->
3. 安装Toolbox好了，下一步：
![](http://docs.daocloud.io/user/pages/03.faq/08.install-docker-daocloud/DashboardDaoCloudInstall.png)
4. `$ docker-machine start default`启动 Docker.
当执行后提示：`Host does not exist: "default"`
{% codeblock docker-machine create http://stackoverflow.com/a/38602630 stackoverflow %}
docker-machine create -d virtualbox default
{% endcodeblock %}
5. 在 `Docker 主机` DaoCloud 加速器的组件包：
```ruby
curl -sSL https://get.daocloud.io/daomonit/install.sh | sh -s d0312f829e9807ee0bf157******
```
6. 启动组件包,会在「安装主机监控程序」的 DaoCloud 控制台页面下方显示一台已经接入的主机。
7. 执行`Dao Pull`命令,高速下载`Docker Hub`镜像文件
这台 Docker 主机已经被接入 DaoCloud 平台，用户可以在 DaoCloud 控制台的「我的集群」页面发现这台主机，可以执行管理和部署应用的操作。

### 从Docker Hub 仓库中获取一个镜像
---
Docker 使用类似 git 的方式管理镜像。通过基本的镜像可以定制创建出来不同种应用的 Docker 镜像。Docker Hub 是 Docker 官方提供的镜像中心。在这里可以很方便地找到各类应用、环境的镜像。由于 Docker 使用联合文件系统，所以镜像就像是夹心饼干一样一层层构成，相同底层的镜像可以共享。所以 Docker 还是相当节约磁盘空间的。要使用一 个镜像，需要先从远程的镜像注册中心拉取，这点非常类似 git。
```ruby
docker pull ubuntu
```

## Docker 命令创建管理容器
---
### 获取镜像的两种方式
---
#### 1. `docker pull`命令
```ruby
docker search perfectlysoft/ubuntu
docker pull perfectlysoft/ubuntu
```
#### 2. `docker import`命令
```ruby
docker import myubuntu.tar.gz
```
### 通过镜像创建容器
---
创建一个容器有两种方式：
```ruby
docker create 镜像名
docker run   镜像名   //立即启动容器 等价于：create + start 命令组合
```
进入容器终端控制台如下：
```
root@ec72dc76502e:/# ls
app  boot  etc   lib    media  opt   root  sbin  sys  usr
bin  dev   home  lib64  mnt    proc  run   srv   tmp  var
```
#### `docker run images`命令
1. 样例1
执行`run images`，并将 Ubuntu 的 Shell 作为入口，进入Docker容器环境操作
```ruby
docker run -it ubuntu:latest sh -c '/bin/bash'
```
2. 样例2
执行`docker start -i 容器`命令进入容器环境
```
docker start -i 容器ID/容器名
```
#### 从`Kitematic`GUI进入容器环境 
``` ruby
bash -c "clear && docker exec -it perfectswift sh"
```
> 参数 
  > -i 表示这是一个交互容器，会把当前标准输入重定向到容器的标准输入中，而不是终止程序运行
  > -t 指为这个容器分配一个终端

这时候我们成功创建了一个 Ubuntu 的容器，并将当前终端连接为这个 Ubuntu 的 bash shell。这时候就可以愉快地使用 Ubuntu 的相关命令了。

### ps 查看容器状态
---
在容器运行期间，我们可以通过 `docker ps` 命令看到所有当前正在运行的容器。
添加-a参数可以看到所有创建的容器：
```ruby
docker ps -a
```
### 容器ID标识
---
每个容器都有一个唯一的 ID 标识，通过 ID 可以对这个容器进行管理和操作。在创建容器时，我们可以通过 --name 参数指定一个容器名称，如果没有指定系统将会分配一个，就像这里的「trusting_morse」。

### 启动/退出/移除容器
---
启动：`start`
```ruby
docker start -i trusting_morse
```
> 注意：每次执行 docker run 命令都会创建新的容器，建议一次创建后，使用 docker start/stop 来启动和停用容器。

退出：
按 `Ctrl+D` 退出

移除：`rm`ID/name
```
docker rm [CONTAINER ID/NAMES]
```
重命名：`rename`
```
docker rename 容器ID newName
```
## 创建管理镜像
---
Docker 强大的威力在于可以把自己开发的应用随同各种依赖环境一起打包、分发、运行。要创建一个新的 Docker 镜像，通常基于一个已有的 Docker 镜像来创建。
Docker 提供了两种方式来创建镜像：
1. 把容器创建为一个新的镜像
2. 使用 Dockerfile 创建镜像。

### 将容器创建为镜像
---
1. 为了创建一个新的镜像，我们先创建一个新的容器作为基底：
```ruby
docker run -it ubuntu:latest sh -c '/bin/bash'
```

2. 定制这个容器，例如我们可以配置 PHP 环境、将我们的项目代码部署在里面等：
```ruby
apt-get install php
# some other opreations ...
```
当执行完操作之后，我们按 Ctrl+D 退出容器.
3. 获取定制后的容器ID
```
    docker ps -a
    [root@localhost ~]# docker ps -a

    CONTAINER ID        IMAGE                        COMMAND                    CREATED             STATUS                      PORTS               NAMES
    cb2b06c83a50        ubuntu:latest                "sh -c /bin/bash"          7 minutes ago       Exited (0) 7 seconds ago                       trusting_morse
```
4. 执行`docker commit` 把这个容器变为一个镜像：
```shel
docker commit cb2b06c83a50 ubuntu:myubuntu
```
这时候 docker 容器会被创建为一个新的 Ubuntu 镜像，版本名称为 myubuntu。以后我们可以随时使用这个镜像来创建容器了，新的容器将自动包含上面对容器的操作。    
5. 打包/发布镜像
```ruby
docker save -o myubuntu.tar.gz ubuntu:myubuntu
```
6. 导入打包镜像
```ruby
docker import myubuntu.tar.gz
```

### Dockerfile
---
#### 通过`Dockerfile`创建镜像
```ruby
docker build yourDir/Dockerfile
```
> 确保Dockerfile文件在一个目录中，否则会提示错误

Docker Hub 提供了类似 GitHub 的镜像存管服务。一个镜像发布到 Docker Hub 不仅可以供更多人使用，而且便于镜像的版本管理。在一个企业内部可以通过自建 Docker Registry 的方式来统一管理和发布镜像。将 Docker Registry 集成到版本管理和上线发布的工作流之中，还有许多工作要做，在我整理出最佳实践后会第一时间分享。
使用命令行的方式创建 Docker 镜像通常难以自动化操作。在更多的时候，我们使用 Dockerfile 来创建 Docker 镜像。Dockerfile 是一个纯文本文件，它记载了从一个镜像创建另一个新镜像的步骤。撰写好 Dockerfile 文件之后，我们就可以轻而易举的使用 docker build 命令来创建镜像了。
Dockerfile 非常简单，仅有以下命令在 Dockerfile 中常被使用：

|命令       |	参数  |    说明    |
|---------:|---------:|---------:|
|#         |   -      |注释说明    |
|FROM|	<image>[:<tag>]	| 从一个已有镜像创建，例如ubuntu:latest|
|MAINTAINER|	Author <some-one@example.com>|	镜像作者名字，如Max Liu <some-one@example.com>|
|RUN	|<cmd>或者['cmd1', 'cmd2'…]|	在镜像创建用的临时容器里执行单行命令|
|ADD	|<src> <dest>|	将本地的<src>添加到镜像容器中的<dest>位置|
|VOLUME	|<path>或者['/var', 'home']|	将指定的路径挂载为数据卷|
|EXPOSE	|<port> [<port>...]|	将指定的端口暴露给主机|
|ENV	|<key> <value> 或者 <key> = <value>|	指定环境变量值|
|CMD	|["executable","param1","param2"]|	容器启动时默认执行的命令。注意一个Dockerfile中只有最后一个CMD生效。|
|ENTRYPOINT|	["executable", "param1", "param2"]	|容器的进入点|

#### 配置Dockerfile
---
下面是一个 Dockerfile 的例子：
```ruby
# This is a comment
FROM ubuntu:14.04
MAINTAINER Kate Smith <ksmith@example.com>
RUN apt-get update && apt-get install -y ruby ruby-dev
RUN gem install sinatra
```
##### `CMD`命令
---
`CMD`: 命令可用指定 Docker 容器启动时默认的命令
```
docker run -it ubuntu:latest sh -c '/bin/bash'
```
其中 `sh -c '/bin/bash'` 就是手工指定的`CMD`,否则容器将会使用默认 `CMD` 指定的命令启动。
##### `ENTRYPOINT`命令
---
用来指定可执行文件、Shell 脚本，同时会并把启动参数或 CMD 指定的默认值，当作附加参数传递给 执行文件、Shell 脚本。
```ruby
ENTRYPOINT ['/usr/bin/mysql']
CMD ['-h 192.168.100.128', '-p']
```
执行mysql启动程序，连接`192.168.100.128` 主机,也可以通过指定参数，来连接别的主机。

因此，我们在使用 Dockerfile 创建文件的时候，可以创建一个 entrypoint.sh 脚本，作为系统入口。在这个文件里面，我们可以进行一些基础性的自举操作，比如检查环境变量，根据需要初始化数据库等等。下面两个文件是我在日常工作的项目中添加的 Dockerfile 和 entrypoint.sh，仅供参考：
https://github.com/starlight36/SimpleOA/blob/master/Dockerfile
https://github.com/starlight36/SimpleOA/blob/master/docker-entrypoint.sh
在准备好 Dockerfile 之后，我们就可以创建镜像了：
#### 创建镜像
```ruby
docker build -t starlight36/simpleoa .
```

