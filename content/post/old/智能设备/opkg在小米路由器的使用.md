---
title: opkg在小米路由器的使用
date: 2018-10-14 23:27:01
updated: 2018-10-14 23:47:06
comments: true
tags: [智能设备]
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
<!--github库卡片-->
{% github openwrt openwrt 759f111 width = 30% %}
[openwrt.io](https://openwrt.io)
[小米路由器固件信息](https://openwrt.io/docs/miwifi/)
## 

路由器read-only file system怎么改权限 
```
mount -o remount,rw /
```
# 新建配置
1. 备份初始conf
```
mv /etc/opkg.conf /etc/opkg.conf.bak
```
2. 开始配置
`vim /etc/opkg.conf`
```
src/gz attitude_adjustment_base http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/base
src/gz attitude_adjustment_packages http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/packages/
src/gz attitude_adjustment_luci http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/luci/
src/gz attitude_adjustment_management http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/management/
src/gz attitude_adjustment_oldpackages http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/oldpackages/
src/gz attitude_adjustment_routing http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/routing/
src/gz openwrt_dist http://openwrt-dist.sourceforge.net/releases/ramips/packages
src/gz openwrt_dist_luci http://openwrt-dist.sourceforge.net/releases/luci/packages
dest root /data
dest ram /tmp
lists_dir ext /data/var/opkg-lists
option overlay_root /data
arch all 100
arch ramips 200
arch ramips_24kec 300
```
3. 更新库
```
$ opkg update
Downloading http://downloads.openwrt.org/..../generic/packages/packages/Packages.gz.
Updated list of available packages in /data/var/opkg-lists/attitude_adjustment.
```
> 注：如果下载失败，请确认是否是http而非https。另外如果链接失效可能是更新了包，可以到https://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/base/搜索libc_找到最新下载地址。

经过用上操作，opkg就可以正常使用了
### opkg安装命令
`opkg install 软件包名`:一律安装在/data/usr/bin目录中
`opkg upgrade 软件包名`: 升级软件包
`opkg list-installed`:查看已经安装的包
`opkg list-upgradable`:查看可以升级的包
`opkg update` 更新可以获取的软件包列表
`opkg remove 软件包名` 卸载已经安装的指定的软件包
`opkg list` 获取软件列表
1. 安装基础包libc*
安装其他库的时候，经常会提示错误，缺少`libc*`。这里他没办法直接安装，只能手工操作:
```
$  opkg install http://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/base/libc_0.9.33.2-1_ramips_24kec.ipk
```
2. 接下来就可以安装一些自己需求的软件包:
例如：安装git命令工具
```
$ opkg install git
```
5. 配置系统环境
由于没有配置 /etc/profile,导致：运行命令时，提示no fond
例如：#python    no fond
```
export PATH=/data/usr/bin:$PATH
$ source /etc/profile //命令生效
```
[OpenWRT基本知识整理](http://www.liwangmeng.com/openwrt基本知识归纳/)
