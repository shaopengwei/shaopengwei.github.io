---
title: Hadoop Yarn HA 集群部署
date: 2025-05-15 20:30
venue: Tianjin
collection: publications
category: Hadoop
permalink: /publication/Hadoop Yarn HA 集群部署-2025-05-15
excerpt: Hadoop Yarn HA 集群部署
---
# Hadoop Yarn HA 集群部署

### 1. yarn 集群存在的问题
RM 负责资源管理和应用调度，是 yarn 的核心组件，会存在 SPOF（Single point of failure，单点故障）问题。为了解决这个问题，yarn 设计实现了 HA 架构。

### 2. HA 架构设计
在 RM 运行期间，多个 RM 同时存在来增加冗余进而消除单点故障，并且只能有一个 RM 处于 Active 状态，其他 RM 处于 Standby 状态，当 Active 节点无法正常工作，其余 Standby 状态的节点会通过竞争选举产生新的 Active 节点。

HA 架构实现的关键是主备之间状态数据同步和主备之间顺利切换。数据同步问题可以通过 Zookeeper 来存储共享集群的状态数据。主备切换可以手动，也可以利用 Zookeeper 自动实现。

### 3. 主备切换原理
1）在 Zookeeper 上会创建一个 ActiveStandbyElectorLock 的锁节点，所有 RM 在启动的时候，都会去竞争写这个临时的锁节点，Zookeeper 能保证只有一个 RM 写成功。写成功的 RM 会切换为 Active 状态，没成功的 RM 则切换为 Standby 状态。
2）Standby 状态的 RM 向 ActiveStandbyElectorLock 节点注册一个节点变更的 Watcher 监听，利用临时节点的特性（会话结束节点自动消失），能够快速感知到 Active 状态的 RM 的运行情况。
3）当 Active 状态的 RM 出现故障（宕机或网络中断），它在 zk 上创建的锁节点随之被删除，这时其他 Standby 状态的 RM 都会收到监听器 Watcher 的通知，开始竞争写锁节点，创建成功的变为 Active 状态，其他的则为 Standby 状态。

##### 脑裂现象
如果 Active 状态的 RM 出现假死（GC耗时长、网络中断、CPU负载高）情况，导致无法正常对外响应，Standby 的 RM 会重新开始选举产生 Active RM，而这时原来的 RM 又恢复正常了，还认为自己是 Active 状态继续执行它的任务，这样就会存在多个 Active 状态的 RM，导致系统混乱无法执行。这就是分布式系统中常见的脑裂问题。

yarn 的 Fencing （隔离）机制解决了这个问题。它是利用 Zookeeper 数据节点的 ACL 权限控制来实现不同 RM 之间的隔离。创建的锁节点必须携带 Zookeeper 的 ACL 信息，以防止其他 RM 对这个锁节点更新。借助这个机制，假死后的 RM 试图去更新 Zookeeper 的锁节点相关信息时，会发现没有权限，已经被新的 RM 占据，这样它会把自己切换为 Standby 状态。

### 4. HA 集群配置
修改 yarn-site.xml
```
<!-- ZK 集群 -->
<property>
    <name>hadoop.zk.address</name>
    <value>node1:2181,node2:2181,node3:2181</value>
</property>
<!-- 启用 RM HA 集群 -->
<property>
    <name>yarn.resourcemanager.ha.enabled</name>
    <value>true</value>
</property>
<!-- RM HA 集群标识 ID -->
<property>
    <name>yarn.resourcemanager.cluster-id</name>
    <value>yarn_cluster</value>
</property>
<!-- RM HA 集群中各 RM 的逻辑标识 -->
<property>
    <name>yarn.resourcemanager.ha.rm-ids</name>
    <value>rm1,rm2</value>
</property>
<!-- rm1 运行主机 -->
<property>
    <name>yarn.resourcemanager.hostname.rm1</name>
    <value>node1</value>
</property>
<!-- rm2 运行主机 -->
<property>
    <name>yarn.resourcemanager.hostname.rm2</name>
    <value>node2</value>
</property>
<!-- rm1 WebUI 地址 -->
<property>
    <name>yarn.resourcemanager.webapp.address.rm1</name>
    <value>node1:8088</value>
</property>
<!-- rm2 WebUI 地址 -->
<property>
    <name>yarn.resourcemanager.webapp.address.rm2</name>
    <value>node2:8088</value>
</property>
<!-- 开启自动故障转移 -->
<property>
    <name>yarn.resourcemanager.ha.automatic-failover.enabled</name>
    <value>true</value>
</property>
```