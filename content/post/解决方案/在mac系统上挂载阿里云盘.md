+++
title = "在mac系统上使用webdav工具挂载阿里云盘"
description = "扩充电脑硬盘空间的一种方式，阿里云盘不限速下载，可以作为本地盘来使用，本地应用可以直接访问阿里云盘的资源文件。"
date = 2021-10-19T18:49:00+08:00
publishDate = 2021-10-19T16:47:00+08:00
lastmod = 2021-10-19T18:50:08+08:00
categories = ["解决方案"]
draft = false
authors = ["iTBoyer"]
+++

1.  阿里云盘挂在到本地硬盘
2.  安装webdav 工具  
    1.  安装docker  
        
        docker mac 版本，直接只用官网dmg 安装。  
        
        使用brew 安装，会出现缺少组件问题：[Cannot connect to the Docker daemon on macOS](https://stackoverflow.com/questions/44084846/cannot-connect-to-the-docker-daemon-on-macos)
    
    2.  message/aliyundriver-webdav 安装  
        1.  在 `dotfiles/docker/aliyundrive-webdav/compose.yaml`  
            
            {{< highlight yaml "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                           version: "3.0"
                           services:
                             webdav-aliyundriver:
                               image: messense/aliyundrive-webdav
                               container_name: aliyundriver
                               environment:
                                 - REFRESH_TOKEN=9d3535a***d8e85
                                 - WEBDAV_AUTH_USER=admin
                                 - WEBDAV_AUTH_PASSWORD=admin
                               volumes:
                                 - /Users/boyer/docker/alidriver/:/etc/aliyun-driver/
                               ports:
                                 - 8686:8080
                               restart: unless-stopped
            {{< /highlight >}}
        
        2.  docker-compase 使用  
            
            使用docker-compose 可以方便管理镜像中的环境变量  
            
            1.  创建镜像，启动容器在 compose目录下，执行：  
                
                {{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                                     docker-compose up -d
                {{< /highlight >}}
            
            2.  更新镜像环境属性  
                
                {{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
                            #关闭 Docker 容器们
                            docker-compose down
                            # 删除已停止的 Docker 容器
                            docker-compose rm
                            # ……
                            # 修改 docker-compose 配置文件
                            # ……
                
                            # 再次开启 Docker 服务
                            docker-compose up -d
                {{< /highlight >}}
3.  挂载云盘  
    
    方式1: mac 端原生挂载方式：使用 cmd+k 访问：webdav路径： `http://localhost:8686`  
    
    方式2: rclone 挂在云盘到本本地 oneDrive/aliyunpan
