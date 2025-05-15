---
title: Zookeeper 服务器搭建
date: 2025-05-15 20:32
venue: Tianjin
collection: publications
category: Zookeeper
permalink: /publication/Zookeeper 服务器搭建-2025-05-15
excerpt: Zookeeper 服务器搭建
---
# Zookeeper 服务器搭建

### 1.下载解压 Zookeeper 安装包
国内清华源：`https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/`
解压安装包


### 2.环境要求
JDK

### 3.配置文件
1) 配置文件：conf/zoo.cfg

| 字段名 | 说明 |
|---|---|
| tickTime | leader、follower心跳检测时间，单位ms |
| initLimit | follower与leader建立连接超时时间，initLimit*tickTime |
| syncLimit | leader下发给follower命令操作反馈时间syncLimit*tickTime |
| dataDir | 持久化日志、快照等数据的目录 |
| clientPort | 客户端连接zookeeper使用的端口号 |
| maxClientCnxns | 允许最大的连接数 |
| autopurge.snapRetainCount | 快照文件保存数量 |
| autopurge.purgeInterval | 快照清理频率，单位小时 |

增加集群配置：
格式为server.A=B:C:D。
A为集群中服务节点标识
B为服务节点的IP
C为集群内机器间通信端口
D为leader选举端口
```
server.1=node1:2888:3888
server.2=node2:2888:3888
server.3=node3:2888:3888
```

2) 创建 myid 文件
进入 dataDir 路径创建 myid 文件，其中包含节点编号。

### 4.启动 Zookeeper
`./bin/zkServer.sh start`
`./bin/zkServer.sh status`

### 5.常用命令
***服务端操作命令：***
启动服务：./zkServer.sh start
查看服务状态：./zkServer.sh status
停止服务：./zkServer.sh stop
前台的方式启动服务：./zkServer.sh start-foreground
服务重启：./zkServer.sh restart
客户端连接zk：，默认连接本机./zkCli.sh
连接指定zk：./zkCli.sh -server node1:2181

***客户端操作命令：***
查看文件状态：stat path watch
更新文件数据：set path data version
显示文件：ls path watch
显示操作记录：history
删除文件：delete path version
删除文件数据：get path watch
创建文件 -s为持久文件  -e为临时文件：create -s path data acl
客户端退出：quit


