+++
title = "APPlink 的使用"
lastmod = 2020-07-19T21:40:54+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

增加 web 端打开短视频 APP  
参考教程：<http://docs.getui.com/getui/mobile/ios/applink/>  

-   [X] 协调李军峰在分享组件中添加 SchemeUrl 配置字段协商之后，使用分享组件添加 schemeUrl，目的为了便于其他 APP 快速支持第三方打开 APP
-   [X] 配置短视频支持第三方打开  
    1.  在短视频 APP 中，添加分享组件，设置 schemeUrl 值：/shortVideoCode
    2.  打包，查看配置结果正式环境：<https://jhbac.iuoooo.com/apple-app-site-association>  
        测试环境：<http://testbac.iuoooo.com/apple-app-site-association>
    3.  在 Xcode 配置 APP 的 Associated Domains  
        测试环境：applinks:testbac.iuoooo.com  
        正式环境：applinks:jhbac-ui.iuoooo.com
    4.  验证第三方打开测试环境：暂不支持测试，需要把 Associated Domains 的测试 links 同步到主工程才行。暂时不考虑测试地址:<https://testbac.iuoooo.com/shortVideoCode>  
        正式环境：平台下载包安装到手机之后，正式地址：<https://jhbac.iuoooo.com/shortVideoCode>  
        使用 Safari 访问下面路径，既可打开短视频 APP  
        1.  当 APP 已经安装自动启动 applink 所指向的 APP，甚至可以定位到 app 的具体页面。
        2.  当没安装 APP  
            Safari 会自动打开 applink 指向的网页。需要 web 服务器在改地址下提供一个有效的网页（自动跳转到 AppStore 或者 web 端设计一套下载引导页）
