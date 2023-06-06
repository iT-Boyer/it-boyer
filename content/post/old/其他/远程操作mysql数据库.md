---
title: 远程操作mysql数据库
date: 2017-05-24 17:07:47
updated: 2018-10-05 21:42:35
comments: true
tags: [数据库]
categories:
- 学习笔记
keywords: 
permalink: 
---

## 腾讯云服务器
1. 申请账户，体验5天，搭建一个jira服务，还有微信小程序服务
    
2. 需要在服务器上安装docker （腾讯服务器提供了一款支持docker的Ubuntu系统）
3. 想使用daocloud加速器，就要更新docker版本
docker run --detach --publish 8080:8080 cptactionhank/atlassian-jira:latest
http://[dockerhost]:8080 and finish the configuration

### 远程服务器的基本操作
1. 登录：`ssh -l username 服务ip／域名`
    ssh ubuntu@123.***.*.*6   5******RtH
    sudo docker pull cptactionhank/atlassian-jira
2. 服务器端拷贝文件目录
    scp -i localDir serveruser@serverip:serverDir

### docker下安装mysql数据库镜像

```sh
sudo docker pull mysql 
sudo docker run --name jiradb -e MYSQL_ROOT_PASSWORD=jiradb -d mysql/mysql-server:latest
```
> -name : 容器名
MYSQL_ROOT_PASSWORD : 数据库密码
-d : 镜像名:tag 版本

#### 终端：单行登录mysql
```
sudo docker exec -it jiradb mysql -ujira -pjira
```

#### 进入mysql终端,访问数据库  
```
sudo docker exec -it jiradb bash
#登录数据库  默认用户root 密码为空，如果前边设置了MYSQL_ROOT_PASSWORD的值，则需要密码
mysql -uroot -p   #登录本地数据库 可以 省略-h参数 -h 127.0.0.1
回车
输入密码：jiradb  #就是$MYSQL_ROOT_PASSWORD的值
即登录
```
#### 用户权限控制
1. 查看sql服务器的状态：
```
status;
```
2. 创建用户名
```
create user jira identified by 'jira';
```
3. 赋予权限
```
grant all privileges on *.* to 'jira'@'%' identified by 'jira' with grant option;
grant all privileges on *.* to 'jira'@'localhost' identified by 'jira' with grant option;
flush privileges;
quit;
```
#### 数据库操作
1. 创建数据库
```
create database jiradb character set 'UTF8';
```
2. 查看当前数据库名：
```
select database();
```
3. 切换指定数据库
```
use jiradb;
```
4. 查看数据库表
```
show tables;
```
创建表

##### jira和数据库关联结果
无法通过 docker 中mysql镜像的盒子来实现jira和数据库关联：
从另一个容器中的应用来访问jiradb容器中的mysql服务：没成功
```
Connect to MySQL from an application in another Docker container
sudo docker run --name jirad --link jiradb:mysql/mysql-server -d cptactionhank/atlassian-jira:latest
````

最终采用在ubuntu系统中安装mysql：
```
$sudo apt-get -y install mysql-server
```


##问题：Could not reach any registry endpoint
安装Linux加速器：
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://142900b5.m.daocloud.io
该脚本可以将 --registry-mirror 加入到你的 Docker 配置文件 /etc/default/docker 中。适用于 Ubuntu14.04、Debian、CentOS6 、CentOS7、Fedora、Arch Linux、openSUSE Leap 42.1，其他版本可能有细微不同。
ubuntu 系统安装daocloud检测工具：
curl -sSL https://get.daocloud.io/daomonit/install.sh | sh -s d0312f829e9807ee0bf157cdc9c9cca42380395c 

### 更新服务器上的docker
[官网教程](https://docs.docker.com/engine/installation/linux/ubuntulinux/#/prerequisites-by-ubuntu-version)
### Update your apt sources
To set APT to use packages from the Docker repository:
1. Log into your machine as a user with sudo or root privileges.
2. Open a terminal window.
3. Update package information, ensure that APT works with the https method, and that CA certificates are installed.
```bash
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates
```
4. Docker资源库
|Ubuntu version|	Repository|
|---|----|
|Precise 12.04 (LTS)|deb https://apt.dockerproject.org/repo ubuntu-precise main|
|Trusty 14.04 (LTS)	|deb https://apt.dockerproject.org/repo ubuntu-trusty main|
|Wily 15.10         |deb https://apt.dockerproject.org/repo ubuntu-wily main|
|Xenial 16.04 (LTS)	|deb https://apt.dockerproject.org/repo ubuntu-xenial main|
>Note: Docker does not provide packages for all architectures. Binary artifacts are built nightly, and you can download them from https://master.dockerproject.org. To install docker on a multi-architecture system, add an [arch=...] clause to the entry. Refer to Debian Multiarch wiki for details.
5. 导入库 
<REPO> = deb https://apt.dockerproject.org/repo ubuntu-precise main
```bash
$ echo "<REPO>" | sudo tee /etc/apt/sources.list.d/docker.list
```
`
6.Update the APT package index.
```
$ sudo apt-get update
```
7.Verify that APT is pulling from the right repository.
When you run the following command, an entry is returned for each version of Docker that is available for you to install. Each entry should have the URL https://apt.dockerproject.org/repo/. The version currently installed is marked with ***.The output below is truncated.

$ apt-cache policy docker-engine

docker-engine:
Installed: 1.12.2-0~trusty
Candidate: 1.12.2-0~trusty
Version table:
*** 1.12.2-0~trusty 0
500 https://apt.dockerproject.org/repo/ ubuntu-trusty/main amd64 Packages
100 /var/lib/dpkg/status
1.12.1-0~trusty 0
500 https://apt.dockerproject.org/repo/ ubuntu-trusty/main amd64 Packages
1.12.0-0~trusty 0
500 https://apt.dockerproject.org/repo/ ubuntu-trusty/main amd64 Packages

From now on when you run apt-get upgrade, APT pulls from the new repository.

To upgrade your kernel and install the additional packages, do the following:

Open a terminal on your Ubuntu host.
Update your package manager.
$ sudo apt-get update
Install both the required and optional packages.
$ sudo apt-get install linux-image-generic-lts-trusty
Repeat this step for other packages you need to install.
Reboot your host to use the updated kernel.
$ sudo reboot
After your system reboots, go ahead and install Docker.

Install the latest version
Make sure you have satisfied all the prerequisites, then follow these steps.

Note: For production systems, it is recommended that you install a specific version so that you do not accidentally update Docker. You should plan upgrades for production systems carefully.
Log into your Ubuntu installation as a user with sudo privileges.
Update your APT package index.
$ sudo apt-get update
Install Docker.
$ sudo apt-get install docker-engine
Start the docker daemon.
$ sudo service docker start
Verify that docker is installed correctly by running the hello-world image.
$ sudo docker run hello-world
This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.
