---
title: Kindle之ResilioSync索引
date: 2017-01-23 12:49:49
updated: 2019-01-16 21:01:15
comments: true
tags: [资源]
categories:
- 解决方案
keywords: kindle,书籍,资源,ResilioSync
permalink: 
---

## Kindle伴侣精品书库 

[Kindle伴原文](https://kindlefere.com/share)
Resilio Sync同步密钥
{% codeblock lang:basnh %}
BOC3NIGPF2DOKETOF2FAHXJXE2HF24QWC
{% endcodeblock %}

### 精品书库
[精品库](https://kindlefere.github.io/share/ebook/)
{% iframe https://kindlefere.github.io/share/ebook/ 800 400 %}

### 每周一书
[每周一书](https://kindlefere.github.io/share/weekly/index.html)
{% iframe https://kindlefere.github.io/share/weekly/index.html 800 400 %}

### 国外书籍
[国外精选](https://kindlefere.github.io/share/ebook-en/index.html)
{% iframe https://kindlefere.github.io/share/ebook-en/index.html 800 400 %}


## Resilio Sync 
[原地址详解](https://kindlefere.com/post/347.html)

### 实现原理
Resilio Sync 这款软件的优点上面已经说了一些，就是不依赖中心服务器，所有的文件都分布在用密钥连接在一起的电脑上。这里重点说一下小伙伴们关心的缺点。

缺点一：免费版只能强制同步所有文件，比如书库的大小有 5G，只能将这 5G 的电子书全部同步到你的电脑上，这需要你有一块足够大的硬盘。除非你升级到收费版（每年 100 元）实现选择性同步。

缺点二：因为没有中心服务器，所以下载的速度依赖于每个电脑的上传速度，和中心服务器的分享方式相反，人越多同步的速度就越快，反之，人越少同步的速度就越慢。

### 在IgnoreList文件中忽略不想同步的文件
BitTorrent Sync还支持文件过滤，如果你有一些文件不想被同步，你可以通过配置`IgnoreList`实现。   
`IgnoreList`是一个UTF-8编码的txt文件，里面你可以定义单个文件，路径，以及规则，他支持简单的“？”和“*”匹配。  
```
cd 同步目录/.sync／
cat IgnoreList 
```
    ># IgnoreList is a UTF-8 encoded .txt file that helps you specify single files, paths and rules 
    ># for ignoring during the synchronization job. It supports "?" and "*" wildcard symbols.
    #
    #
    # OS generated files #
    .DS_Store
    .Spotlight-V100
    .Trashes
    ehthumbs.db
    desktop.ini
    Thumbs.db
    # Temporary files #
    ~*
    *~
    .~lock.*
    *.part
    *.crdownload
    @eaDir
    @SynoResource
    .@__thumb

### 高级设置相关说明

```ruby
disk_low_priority：true  设置在磁盘上操作文件的优先级，如果设置为false，在同步文件时读写文件将会采用最高速度和优先级，不过这样会影响其他应用的性能。

folder_rescan_interval：600  设置扫描目录的时间间隔，单位为秒

lan_encrypt_data：true  如果设置为ture，则在本地网络传输时会采用加密传输。

lan_use_tcp：false  如果设置为ture，在本地网络同步会采用tcp传输，而不是采用udp传输。注意：在LAN中禁止加密并采用tcp传输，会增加传输速度。

rate_limit_local_peers：false  申请在本地网络的peers直接限速传输，默认没有在LAN里面限速

send_buf_size：5  在发送文件时可以使用的发送缓存，可以设置1~100M

recv_buf_size：5  在接收文件时可以使用的接收缓存，可以设置1~100M

sync_max_time_diff：600  同步的设备之间的时间差别

sync_trash_ttl：30  设置多少天之后自动删除.SyncArchive目录中的文件

max_file_size_diff_for_patching：1000

max_file_size_for_versioning：1000  版本控制的一个参数，不了解...
```

### 常见问题汇总

#### 添加同步链接后为什么找不到节点？

如果是刚添加同步密钥或链接，请稍等片刻。如果很长时间仍然找不到节点无法同步，请尝试：把已经添加的同步目录删除，在 Sync 界面上谭家的同步断开，然后重新添加同步密钥或同步链接。

#### 提示“与 x 个用户的时间差”怎么办？

如果系统的时间严重不准会导致 BT Sync 无法正常工作。如果 Sync 软件提示的事您的电脑有时间差，请确保开启自动时间同步，如果时间同步没问题，请先退出 Sync 软件重新开启。如果提醒其他人有时间差，请忽略。

#### 为什么同步的电子书比目录标示的大？

书库在维护的过程中会删除一些质量较差和重复的电子书，如果被删除的电子书已经同步到你的电脑上，就会被 Sync 自动备份下来。备份位置在同步目录下的隐藏文件夹 .sync/Archive 中，在 Sync 软件中右键点击同步文件夹，在弹出的菜单中点击“打开存档文件”即可打开。

默认情况下，此目录中的文件 30 天后会自动删除。书库中删除的文件没有保留的必要，为了避免备份文件占用空间，可以禁用此功能。先把 Archive 文件夹删除，然后在 Sync 软件中右键点击同步文件夹，在弹出的菜单中点击“首选项”，取消“在文件夹存档中存储已删除的文件”前面的勾选即可。

#### 开启 Sync 后电脑变得很卡怎么办？

因为 Sync 传输数据时需要读写硬盘，这可能会导致电脑其它的数据读取变慢，解决方法就是设置限速，让同步细水长流。打开 Sync 的软件的“首选项”，在“高级”选项卡中找到“限制接收速率”和“限制发送速率”，输入一个合适的值即可。另外，如果正在做其他工作，建议暂停或暂时退出 Sync 软件。

另外，在“高级”界面里，点击底部的“打开高级用户偏好设置”，把“disk_low_priority”这项设置为 true 也可以缓解卡顿的现象。其中“rate_limit_local_peers”是设置在磁盘上操作文件的优先级，默认为 false，在同步文件时读写文件将会采用最高速度和优先级，所以会影响其他应用的性能。

#### 重新添加能继续用之前的同步目录吗？

有时因为某种原因，导致添加到 Resilio Sync 的同步丢失，需要重新添加密钥，这种情况下，是可以继续使用之前的同步目录的，只需要在重新添加密钥选择同步目录的时候，选择原来的目录即可。
