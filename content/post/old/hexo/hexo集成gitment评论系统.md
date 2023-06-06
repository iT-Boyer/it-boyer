---
title: hexo集成gitment评论系统
date: 2018-10-03 00:54:35
updated: 2018-10-04 09:04:35
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

### 安装`gitment`
在`package.json`文件添加gitment依赖：
```
"gitment": "^0.0.3"
```
安装
```
npm install
```
### 申请应用
首先去[New OAuth App](https://github.com/settings/applications/new)为你的博客应用一个密钥:
```
Application name:随便写
Homepage URL:这个也可以随意写,就写你的博客地址就行
Application description:描述,也可以随意写
Authorization callback URL:这个必须写你的博客地址
```
申请好之后点注册,然后就可以看到两个东西ClientID和Client Secret,后面会用到.
### 配置
下面就是配置`Gitment`,主要编辑在`themes/next/_config.yml`:
```js
# Gitment
# Introduction: https://imsun.net/posts/gitment-introduction/
gitment:
    enable: true
    mint: true # RECOMMEND, A mint on Gitment, to support count, language and proxy_gateway
    count: true # Show comments count in post meta area
    lazy: false # Comments lazy loading with a button
    cleanly: false # Hide 'Powered by ...' on footer, and more
    language: # Force language, or auto switch by theme
    github_user: {you github user id}
    github_repo: 随便写一个你的公开的git仓库就行,到时候评论会作为那个项目的issue
    client_id: {刚才申请的ClientID}
    client_secret: {刚才申请的Client Secret}
    proxy_gateway: # Address of api proxy, See: https://github.com/aimingoo/intersect
    redirect_protocol: # Protocol of redirect_uri with force_redirect_protocol when mint enabled
```
### 开通评论
注意到这里基本上已经OK了,再看你的博客应该可以显示评论了.不过每篇博客都需要你手动初始化评论功能(如果你的历史博客很多那就一篇一篇去点吧，不过貌似有人写了批量处理脚本,没试过哈).

### 问题
[object ProgressEvent](https://github.com/imsun/gitment/issues/170)
由于引入的 gitment.js 中有这样的一段代码：
```js
_utils.http.post('https://gh-oauth.imsun.net', {
        code: code,
        client_id: client_id,
        client_secret: client_secret
    }, '').then(function (data) {
        _this.accessToken = data.access_token;
        _this.update();
    }).catch(function (e) {
        _this.state.user.isLoggingIn = false;
        alert(e);
});
```
请求了一个服务接口，由于这个跨域服务接口是作者自己搭建的，已经停止了。
有博主 fork 源作者的 repo ，在这个基础上改的，修改了一些地方接入自己通用的服务[CORS-Proxy-Server](https://github.com/jjeejj/CORS-Proxy-Server).
现在直接把这个文件 `https://imsun.github.io/gitment/dist/gitment.browser.js` 替换为 `https://www.wenjunjiang.win/js/gitment.js` 就可以了.
原理：
hexo 这个评论接了一个自己写的一个通用的跨域服务 `https://cors.wenjunjiang.win/` github地址为：`https://github.com/jjeejj/CORS-Proxy-Server `，代替作者的 `https://gh-oauth.imsun.net` 这个接口地址，去请求 github的接口。 可以自己搭建，可以转发所有的前端跨域服务。要注意的是： 在向 github 请求 `access_token`时 需要带上`Accept: application/json` 或者` Accept: application/xml `请求头， 否则回报 406 的错误.

### 参考
[gitment评论模块接入hexo](https://www.wenjunjiang.win/2017/07/02/gitment%E8%AF%84%E8%AE%BA%E6%A8%A1%E5%9D%97%E6%8E%A5%E5%85%A5hexo/)
[hexo博客配置-添加评论系统-gitment和valine-需注册](https://xiaotiandi.github.io/publicBlog/2018-09-19-d196c9ad.html)
[Gitment：使用 GitHub Issues 搭建评论系统](https://imsun.net/posts/gitment-introduction/)
