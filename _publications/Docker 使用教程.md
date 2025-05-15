---
title: Docker 使用教程
date: 2025-05-15 20:28
venue: Tianjin
collection: publications
category: Docker
permalink: /publication/Docker 使用教程-2025-05-15
excerpt: Docker 使用教程
---
# Docker

### 1. Docker 安装
1）选择对应 Linux 系统版本的安装文件
2）卸载旧版本 Docker
3）配置系统程序源（yum源 apt源等）
4）使用安装命令安装
5）配置 docker 国内镜像源

### 2. Docker 常用命令（以 centos 系统为例）
systemctl enable docker    # 创建 Docker 启动
systemctl start docker     # 启动 Docker
systemctl daemon-reload    # 重新加载进程
systemctl restart docker   # 重启 Docker 进程

===============================镜像操作命令=======================================
docker pull                # 从远端仓库拉取镜像到本地
docker push                # 将本地镜像推送到远端仓库
docker images              # 查看当前所有 docker 镜像
docker rmi                 # 删除本地镜像
docker build               # 根据 docker file 文件构建本地镜像
docker save                # 保存本地镜像到文件
docker load                # 将文件加载成镜像
===============================容器操作命令=======================================
docker ps                  # 当前运行中的容器
          --format         # 格式化输出容器信息
docker rm                  # 删除容器，只能删除未运行的容器，加 -f 强制删除运行中的容器
docker run                 # 创建并运行一个容器
           -d              # 后台创建并运行一个容器
           --name xxx      # docker 容器名字
           -p port:port    # 宿主机端口：容器端口，端口映射
           -v 数据卷名称:容器内目录            # 创建数据卷，并挂载到容器内目录，修改数据卷对应目录中的内容时，容器内目录内容也会相应变化
           (-v /本地目录绝对路径:容器内目录     # 指定本地目录与容器目录挂载，方便本地文件内容的复用)
           --network       # 指定连接的网络
           -e key=value    # 镜像对应的环境变量，文档中有介绍环境变量
           镜像名:版本号     # 使用的镜像和对应的版本号
docker stop                # 停止运行容器
docker start               # 启动容器
docker logs                # 查看容器日志
docker exec                # 在 docker 容器中执行命令
===============================数据卷操作命令=======================================
docker volume create       # 创建数据卷
docker volume ls           # 查看数据卷
docker volume rm           # 删除数据卷
docker volume inspect      # 查看数据卷详情
docker volume prune        # 清除数据卷
===============================自定义镜像=======================================
镜像定义：
    镜像的分层（layer）结构：
                应用
                设置启动脚本
                拷贝执行文件
                环境变量
                操作系统
    dockerfile：文本文件，说明要执行的操作来构建镜像。
        FROM：       指定镜像文件，指定基础镜像等
        ENV：        设置环境变量
        COPY：       拷贝本地文件到镜像到指定目录
        RUN：        执行shell命令，包括解压、拷贝命令等
        EXPOSE：     指定容器运行时监听的端口号
        ENTRYPOINT： 镜像中应用的启动命令，容器运行时调用，例java -jar xxx.jar等
    <<网上查找模板文件修改>>
    构建镜像：docker build -t repository:tag /path/to/dockerfile
===============================容器之间网络通信=======================================
docker inspect             # 查看容器详情
默认情况下，所有容器都是以 bridge 方式连接到 Docker 的一个默认的虚拟网桥上，
docker0: 172.17.0.1
容器1:    172.17.0.2
容器2:    172.17.0.3
自定义网络的容器可以通过容器名互相访问，Docker 的网络操作命令：
docker network create         # 创建一个网络
docker network ls             # 查看所有网络
docker network rm             # 删除指定网络
docker network prune          # 清楚未使用的网络
docker network connect        # 使某个容器连接到某个网络
docker network disconnect     # 使某个容器断开某个网络
docker network inspect        # 查看网络详细信息
===============================DockerCompose=======================================
通过一个单独的 docker-compose.yml模板文件来定义一组相关联的应用容器，帮助我们实现多个相互关联的 Docker 容器的快速部署。
<<网上查找文件模板做相应修改>>

docker compose 
                -f docker-compose-file 
                -p 项目名称 
                -d                        # 后台运行
                up                        # 创建并启动所有容器
                down                      # 停止并移除所有容器和网络
                start                     # 启动容器
                stop                      # 停止容器
                restart                   # 重启容器
                top                       # 查看运行中的进程
                ps                        # 所有启动的容器
                logs                      # 查看容器日志

### 3. Docker 常用概念
Docker Hub：Docker 官方公共仓库
Docker daemon：本地 Docker 服务进程
数据卷：volume 是一个虚拟目录，是容器内目录与宿主机目录之间映射的桥梁
