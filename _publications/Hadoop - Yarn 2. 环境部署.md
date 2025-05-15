---
title: Hadoop Yarn 环境部署
date: 2025-05-15 20:29
venue: Tianjin
collection: publications
category: Hadoop
permalink: /publication/Hadoop Yarn 环境部署-2025-05-15
excerpt: Hadoop Yarn 环境部署
---
# Hadoop Yarn 环境部署

### 1.服务器基础环境准备
1）关闭防火墙
添加 sudoer
`sudo vim /etc/sudoers` 找到 root ALL 后新增一行
关闭防火墙
`sudo systemctl stop firewalld`
禁止防火墙开启自启动
`sudo systemctl disable firewalld` 
查看防火墙状态
`sudo systemctl status firewalld`

2）ssh 免密登录
配置机器名登录
`sudo vim /etc/hosts` 新增 ip 与机器名的映射
生成ssh key，并拷贝到每一台机器上，这里注意也要拷贝到本机，这样 ssh authorizedkey 中也会有本机信息，也能免密登录本机。
`ssh-keygen`
`ssh-copy-id -i id_rsa.pub work@node1`
`ssh-copy-id -i id_rsa.pub work@node2`
`ssh-copy-id -i id_rsa.pub work@node3`

3）集群时间同步
配置国内 yum 源
    1. 备份原来的 yum 源
    `mv /etc/yum.repos.d /etc/yum.repos.d.bak && mkdir /etc/yum.repos.d`
    2. 获取国内 yum 源
    阿里源：`wget http://mirrors.aliyun.com/repo/Centos-7.repo`
    epel源：`wget https://mirrors.aliyun.com/repo/epel-7.repo`
    3. 清除系统 yum 缓存
    `yum clean all`
    4. 生成 yum 缓存
    `yum makecache`
ntp 和 ntpdate 的区别
    ntpd在实际同步时间时是一点点的校准过来时间的，最终把时间慢慢的校正对。ntpdate不会考虑其他程序是否会阵痛，直接调整时间。
    使用ntpd服务，要好于ntpdate加cron的组合。
配置 ntp 同步阿里云
    1. 安装 ntp
    `yum install ntp`
    2. 配置 ntp，使用阿里云服务器时间同步
    `vim /etc/ntp.conf`
    3. 启动 ntpd，查看服务状态
    `systemctl start ntpd`
    `systemctl enable ntpd`
    `systemctl status ntpd`
    4. 查看 ntp 同步状态
    `ntpq -p`

4）部署 JDK
    1. 下载 JDK
    2. 解压 JDK
    3. 配置 JAVA_HOME


### 2.hadoop 环境部署和配置
##### 1）创建统一的工作目录
数据存储路径
`mkdir -p data`
安装包存储和软件部署路径
`mkdir -p software`

##### 2）上传、解压安装包
下载 hadoop
    使用国内源：`https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/stable/`
上传文件到服务器并解压安装包
    `tar xzvf hadoop-3.4.0.tar.gz`
##### 3) 配置 hadoop 环境
1. 节点分配
    3台机器的集群
    | 服务器 | 运行角色 |
    |-------|-------|
    | node1 | namenode datanode resourcemanager nodemanager |
    | node2 | datanode secondarynamenode nodemanager |
    | node3 | datanode nodemanager |
2. hadoop 安装包目录
    | 目录 | 说明 |
    |-------|-------|
    | bin | Hadoop最基本的管理脚本和使用脚本的目录，这些脚本是sbin目录下管理脚本的基础实现，用户可以直接使用这些脚本管理和使用Hadoop。 |
    | etc | Hadoop配置文件所在的目录 |
    | include | 对外提供的编程库头文件（具体动态库和静态库在lib目录中），这些头文件均是用C++定义的，通常用于C++程序访问HDFS或者编写MapReduce程序。 |
    | lib | 该目录包含了Hadoop对外提供的编程动态库和静态库，与include目录中的头文件结合使用。 |
    | libexec | 各个服务对用的shell配置文件所在的目录，可用于配置日志输出、启动参数（比如JVM参数）等基本信息。 |
    | sbin | Hadoop管理脚本所在的目录，主要包含HDFS和YARN中各类服务的启动/关闭脚本。 |
    | share | Hadoop各个模块编译后的jar包所在的目录，官方自带示例。 |
3. 配置文件概述
    第一类1个：hadoop-env.sh

    第二类4个：xxxx-site.xml，site表示的是用户定义的配置，会覆盖default中的默认配置。
        core-site.xml 核心模块配置
        hdfs-site.xml hdfs文件系统模块配置
        mapred-site.xml MapReduce模块配置
        yarn-site.xml yarn模块配置

    第三类1个：workers
4. 编辑配置文件
    ***hadoop-env.sh***
    ```
    export JAVA_HOME=/path/to/jdk
    
    #文件最后添加
    export HDFS_NAMENODE_USER=work
    export HDFS_DATANODE_USER=work
    export HDFS_SECONDARYNAMENODE_USER=work
    export YARN_RESOURCEMANAGER_USER=work
    export YARN_NODEMANAGER_USER=work
    ```

    ***core-site.xml***
    ```
    <!-- 设置默认使用的文件系统 Hadoop支持file、HDFS、GFS、ali|Amazon云等文件系统 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://node1:8020</value>
    </property>

    <!-- 设置Hadoop本地保存数据路径 -->
    <property>    
        <name>hadoop.tmp.dir</name>
        <value>/export/data/hadoop-3.3.0</value>
    </property>

    <!-- 设置HDFS web UI用户身份 -->
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>root</value>
    </property>

    <!-- 整合hive 用户代理设置 -->
    <property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
    </property>

    <property>
        <name>hadoop.proxyuser.root.groups</name>
        <value>*</value>
    </property>
    <!-- 垃圾桶文件保存时间 -->
    <property>
        <name>fs.trash.interval</name>
        <value>1440</value>
    </property>
    ```

    ***hdfs-site.xml***
    ```
    <!-- 设置SNN进程运行机器位置信息 -->
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>node2:9868</value>
    </property>
    ```

    ***mapred-site.xml***
    ```
    <!-- 设置MR程序默认运行模式： yarn集群模式 local本地模式 -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

    <!-- MR程序历史服务器端地址 -->
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>node1:10020</value>
    </property>
    <!-- 历史服务器web端地址 -->
    <property>
        <name>mapreduce.jobhistory.webapp.address</name
        <value>node1:19888</value>
    </property>

    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>

    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>

    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
    ```

    ***yarn-site.xml***
    ```
    <!-- 设置YARN集群主角色运行机器位置 -->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>node1</value>
    </property>

    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

    <!-- 是否将对容器实施物理内存限制 -->
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
    </property>

    <!-- 是否将对容器实施虚拟内存限制。 -->
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
    </property>

    <!-- 开启日志聚集 -->
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>

    <!-- 设置yarn历史服务器地址 -->
    <property>
        <name>yarn.log.server.url</name>
        <value>http://node1:19888/jobhistory/logs</value>
    </property>

    <!-- 保存的时间7天 -->
    <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
    </property>
    ```

    ***workers***
    ```
    node1
    node2
    node3
    ```
5. 分发安装包
将以上修改的 hadoop 安装包分发到每个节点

6. 配置Hadoop环境变量
`export HADOOP_HOME=/path/to/hadoop/`

##### 4) 启动 hadoop 环境
1. 首次启动HDFS时，必须对其进行格式化操作 `hdfs namenode -format`
2. HDFS集群启动 `start-dfs.sh`
3. HDFS集群关闭 `stop-dfs.sh`
4. YARN集群启动 `start-yarn.sh`
5. YARN集群关闭 `stop-yarn.sh`
6. Hadoop 还支持一键启动所有服务 `start-all.sh`
7. Hadoop 还支持一键关闭所有服务 `stop-all.sh`

***可以使用 jps 命令查看服务启动状态***


### 遇到的问题
1. 通过 Mac 不能 ssh 登录到虚拟机
    排查网络问题，Mac --ping--> 虚拟机 ip 不通，虚拟机 --ping--> Mac 能通，台式机能 ssh 到虚拟机，虚拟机之间能 ssh。这个现象就让人怀疑是 Mac 的问题。网上找了相关 Mac ssh 失败的问题，有说是 openssh 版本太高，不支持 ssh-rsa 加密算法，通过修改 ssh config，没有解决问题。
    后来思考，怀疑是 Mac 根本就找不到虚拟机的 ip。因为 Mac 电脑的 ip 和台式机的 ip 是 192.168.1.X，而虚拟机的 ip 是 192.16.8.161.X，很显然不在相同的网段。于是排查虚拟机的网络设置。虚拟机支持三种网络关系设置，<1>桥接 <2>NAT <3>XXX，了解以后，果断从 NAT 改成桥接，重启虚拟机后，虚拟机的 ip 地址网段改为 192.168.1.X。问题解决。

2. 通过 Mac 上传文件到虚拟机速度慢
    使用 iperf3 测试 
    Mac         与      虚拟机      之间的带宽为    100K，
    虚拟机       与      Mac        之间的带宽为    100M，
    宿主台式机   与       虚拟机      之间的带宽为      1G，
    Mac         与      台式机      之间的带宽为    100M，
    找不到具体原因。早上起来自己就好了...