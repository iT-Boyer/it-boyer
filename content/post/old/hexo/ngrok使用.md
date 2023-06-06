---
title: ngrok使用
date: 2017-09-06 12:23:24
updated: 2019-01-16 21:01:13
comments: true
tags: [hexo]
categories:
- 博客站务
keywords: 
permalink:
---
### 注册
1.注册https://ngrok.com/signup
1. Start by [downloading ngrok](https://ngrok.com/download).
2. Install your authtoken
```
./ngrok authtoken 2tpoyojTzL5w55Y4na5DY_3shJgaMVzjJ*****
```
3. Create your first secure tunnel
```
./ngrok http 80
```
4. Open the web interface at http://localhost:4040 to inspect and replay requests
5. Read the documentation for instructions on advanced features like adding HTTP authentication, setting custom subdomains and more.
### 开启TCP协议
1. 开启TCP实现SSH远程访问. [详见](https://ngrok.com/docs#tcp)
```
./ngrok tcp 22
```
如图：
> 每当执行开启命令，端口就会随机生成最新的。

2. 配置SSH远程工具
自定义SSH名：ngrok
SSH域名地址：0.tcp.ngrok.io  (ngrok提供的免费域名，IP地址ping就变新)
SSH端口号：16335 （每次重新启动时需更新）
用户名称/密码: 电脑管理员账户/密码
3. 手机热点访问远程内网电脑
连接前准备：
    1. 使用“网络实用工具”扫描`0.tcp.ngrok.io`域名，确保当前网络16335端口开启
    2. 备选方案，使用手机热点分享，来访问内网电脑，进行连通测试。
4. 连接成功。
总结：可以使用ngrok客户端，开启tcp协议端口，实现SSH远程控制，在不要求过高的网速和安全，可以不搭建ngrok服务器。

### 强大的tunnel(隧道)工具部署原理
[部署](http://tonybai.com/2015/05/14/ngrok-source-intro/)
ngrok在其github官方页面上的自我诠释是 “introspected tunnels to localhost"，这个诠释有两层含义：
1、可以用来建立public到localhost的tunnel，让居于内网主机上的服务可以暴露给public，俗称内网穿透。
2、支持对隧道中数据的introspection（内省），支持可视化的观察隧道内数据，并replay（重放）相关请求（诸如http请 求）。
因此ngrok可以很便捷的协助进行服务端程序调试，尤其在进行一些Web server开发中。ngrok更强大的一点是它支持tcp层之上的所有应用协议或者说与应用层协议无关。比如：你可以通过ngrok实现ssh登录到内 网主 机，也可以通过ngrok实现远程桌面(VNC)方式访问内网主机。

一、ngrok tunnel与ngrok部署

网络tunnel（隧道）对多数人都是很”神秘“的概念，tunnel种类很多，没有标准定义，我了解的也不多（日常工作较少涉及），这里也就不 深入了。在《HTTP权威指南》中有关于HTTP tunnel（http上承载非web流量）和SSL tunnel的说明，但ngrok中的tunnel又与这些有所不同。

ngrok实现了一个tcp之上的端到端的tunnel，两端的程序在ngrok实现的Tunnel内透明的进行数据交互。
![](http://tonybai.com/wp-content/uploads/ngrok-tunnel.png)
ngrok分为client端(ngrok)和服务端(ngrokd)，实际使用中的部署如下：
![](http://tonybai.com/wp-content/uploads/ngrok-deployment.png)
内网服务程序可以与ngrok client部署在同一主机，也可以部署在内网可达的其他主机上。ngrok和ngrokd会为建立与public client间的专用通道（tunnel）。
