---
title: Hadoop Yarn RM 重启机制
date: 2025-05-15 20:30
venue: Tianjin
collection: publications
category: Hadoop
permalink: /publication/Hadoop Yarn RM 重启机制-2025-05-15
excerpt: Hadoop Yarn RM 重启机制
---
# Hadoop Yarn RM 重启机制

### 1. yarn RM 重启机制
重启机制并不是自动重启的意思，它是指当 RM 出现故障发生重启时，保证原来 yarn 集群中运行的任务历史信息、正在运行的任务能够在 RM 恢复后继续保留和继续执行。如果不开启重启机制，RM 出现故障重启后，之前的任务信息都会消失，正在执行的任务作业也会失败。

### 2. 两种 RM 重启机制方案
1) Non-work-preserving RM restart
不保留工作的 RM 重启，在 Hadoop 2.4.0 实现。

    当 client 提交一个 application 给 RM 时，RM 会将该 ***application 的相关信息***存储起来，具体存储的位置可以在配置文件中指定，可以存储在本地文件系统，也可以存储在 HDFS 或者 Zookeeper 上，此外，RM 也会保存 application 的***最终状态信息***（failed, killed, finished），如果是在安全环境下运行，RM 还会保存相关证书文件。
    当 RM 被关闭后，NM 和 client 由于发现连接不上 RM，会不断向 RM 发送消息，便于能及时确认 RM 是否正常，当 RM 重启后，它会发送一条 re-sync（重新同步）当命令给所有的 NM 和 AM，NM 受到重新同步的命令后会杀死所有正在运行的 containers 并重新向 RM 注册，从 RM 的角度来看，每台重新注册的 NM 跟一台新加入到集群的 NM 是一样的。AM 受到重新同步的命令后会自行将自己杀掉。接下来 RM 会将存储的关于 application 的相关信息读取出来，将在 RM 关闭之前最终状态为***正在运行中的 application 重新提交运行****。

2) Work-preserving RM restart
保留工作的 RM 重启，在 Hadoop 2.6.0 实现。

    与不保留工作不同的地方在于， RM 会记录下 container 的整个生命周期的数据，包括 application 运行的相关数据，资源申请状况，队列资源使用情况等。
    当 RM 重启之后，会读取之前存储的 application 的运行状态的数据，同时发送 re-sync 命令，与第一种方式不同的是， NM 在接收到同步命令后不会杀死正在运行的 containers，而是继续运行 containers 中的任务，同时将 containers 的运行状态发送给 RM，RM 根据自己所掌握的数据重构 container 实例信息和相关 application 运行状态，这样就实现了在 RM 重启后继续执行关闭前的任务。

### 3. RM 状态数据存储
RM 的重启机制本质是将 RM 内部的状态信息写入外部存储介质中。在 RM 启动时会初始化状态信息目录，在 application 运行时会将状态信息写入对应目录。如果 RM 发生故障切换或者重启，可以通过外部存储进行恢复。

RM 状态存储的实现时 RMStateStore 抽象类，yarn 提供了几种实现：
| 实例名 | 说明 |
|---|---|
| MemoryRMStateStore | 基于内存的实现 |
| ZKRMStateStore | 基于 Zookeeper 的实现 |
| FileSystemRMStateStore | 基于本地文件系统或者 HDFS 的实现 |
| LeveldbRMStateStore | 基于 Leveldb 的实现 |
| NullRMStateStore | 空实现 |

### 4. RM 重启机制配置
配置启用 RM 重启机制，使用 Zookeeper 作为状态数据存储。
修改 yarn-site.xml
```
<property>
    <name>hadoop.zk.address</name>
    <value>node1:2181,node2:2181,node3:2181</value>
</property>
<property>
    <name>yarn.resourcemanager.recovery.enabled</name>
    <value>true</value>
</property>
<property>
    <name>yarn.resourcemanager.store.class</name>
    <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
</property>
```