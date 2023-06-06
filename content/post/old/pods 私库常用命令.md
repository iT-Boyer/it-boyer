+++
title = "Cocoapods 使用"
lastmod = 2020-09-12T23:04:50+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

## 集成命令 {#集成命令}


## 配置 spec 索引文件 {#配置-spec-索引文件}

设置本地依赖库的方式:  
库存放地址问题：不支持库外路径例如：~/等，只能在 spec 目录中存放库。在提交到私有仓库的时候需要加上--use-libraries  

1.  依赖 .a 静态库  
    
    s.vendored\_libraries = 'JinherSDK/Libs/\*.a'
2.  依赖 framework  
    
    s.vendored\_frameworks = 'JinherSDK/Libs/\*.framework'


## 实战案例 {#实战案例}


### ORM 私库整理 {#orm-私库整理}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-04-20 一 13:38]</span></span>
-   [X] 配置 ffdb 依赖  
    pod lib create ORM2FMDB
-   [X] 验证  
    pod lib lint ORM2FMDB.podspec  --allow-warnings --verbose
-   [X] 发布  
    pod repo push it-boyer ORM2FMDB.podspec --allow-warnings


### 创建 jinher 的 pod 私库，抽取相关依赖，使用 pod 管理 demo 依赖 {#创建-jinher-的-pod-私库-抽取相关依赖-使用-pod-管理-demo-依赖}

pod lib create JinherSDK  
pod lib lint JinherSDK.podspec --allow-warnings --verbose  
pod repo push it-boyer JinherSDK.podspec --allow-warnings
