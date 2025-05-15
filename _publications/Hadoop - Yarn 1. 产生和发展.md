---
title: Hadoop Yarn 的产生和发展
date: 2025-05-15 20:29
venue: Tianjin
collection: publications
category: Hadoop
permalink: /publication/Hadoop Yarn 的产生和发展-2025-05-15
excerpt: Hadoop Yarn 的产生和发展
---
# Hadoop Yarn 的产生和发展

一个 Hadoop 集群可分解为两个抽象实体：MapReduce 引擎和分布式文件系统。MapReduce 引擎能够在整个集群上执行 Map 和 Reduce 任务并报告结果。Hadoop 分布式文件系统 (HDFS) 通过定义来支持大型文件（其中每个文件通常为 64 MB 的倍数）。

### MRv1
MRv1 包含三个部分：运行时环境（JobTracker 和 TaskTracker）、编程模型（MapReduce）、数据处理引擎（Map Task 和 Reduce Task）。
JobTracker 负责资源和任务的管理与调度，TaskTracker 负责单个节点的资源管理和任务执行。

#### 程序执行流程
当一个客户端向一个 Hadoop 集群发出一个请求时，此请求由 JobTracker 管理。JobTracker 与 NameNode 联合将工作分发到离它所处理的数据尽可能近的位置。NameNode 是分布式文件系统的主系统，提供元数据服务来执行数据分发和复制。JobTracker 将 Map Task 和 Reduce Task 安排到一个或多个 TaskTracker 上的可用插槽中。TaskTracker 与 DataNode（分布式文件系统）一起对来自 DataNode 的数据执行 Map 和 Reduce 任务。当 Map 和 Reduce 任务完成时，TaskTracker 会告知 JobTracker，后者确定所有任务何时完成并最终告知客户作业已完成。

### MRv1 缺陷
1. JobTracker 是 MapReduce 的集中处理点，存在单点故障
2. JobTracker 完成了太多的任务，造成了过多的资源消耗，当 MapReduce Task 非常多的时候，会造成很大的内存开销，潜在来说，也增加了JobTracker fail 的风险，这也是业界普遍总结出老 Hadoop 的 MapReduce 只能支持 `4000` 节点主机的上限。
3. 在 TaskTracker 端，以 MapReduce Task 的数目作为资源的表示过于简单，没有考虑到cpu/内存的占用情况，如果两个大内存消耗的 Task 被调度到了一块，很容易出现 OOM
4. 在 TaskTracker 端，把资源强制划分为 map task slot 和 reduce task slot，如果当系统中只有 map task 或者只有 reduce task 的时候，会造成资源的浪费，也就是前面提到过的集群资源利用的问题。
5. MRv1 只能支持 MapReduce 编程模型。

### MRv2 - Yarn
MRv2 包含三个部分：ResourceManager、NodeManager、ApplicationMaster

1. ResourceManager(RM)
RM 是 Yarn 中的主角，决定系统中所有应用程序之间资源分配的权限。RM 接收用户提交的作业，通过 NodeManager 分配、管理各个机器上的计算资源。资源以 Container 形式存在。
1）Scheduler 调度器 - 资源分配 Containers
    在Yarn中有三种调度器可以选择：FIFO Scheduler先进先出调度器 ，Capacity Scheduler容量调度器，FairS cheduler公平调度器。FIFO Scheduler 把应用按提交的顺序排成一个队列，这是一个先进先出队列，在进行资源分配的时候，先给队列中最头上的应用进行分配资源，待最头上的应用需求满足后再给下一个分配，以此类推。FIFO Scheduler 是最简单也是最容易理解的调度器，也不需要任何配置，但它并不适用于共享集群。大的应用可能会占用所有集群资源，这就导致其它应用被阻塞。在共享集群中，更适合采用 Capacity Scheduler 或 Fair Scheduler，这两个调度器都允许大任务和小任务在提交的同时获得一定的系统资源。
2）ApplicationManager - 接收 Job 提交请求，分配第一个 Container 来运行 ApplicationMaster 并监控 ApplicationMaster 状态

2. NodeManager(NM)
NM 是 Yarn 中的从角色，NodeManager 管理 YARN 集群中的每个节点。NodeManager 提供针对集群中每个节点的服务，从监督对一个容器的终生管理到监视资源和跟踪节点健康，并定时向 RM 汇报资源使用情况。MRv1 通过插槽管理 Map 和 Reduce 任务的执行，而 NodeManager 管理抽象容器（Container），这些容器代表着可供一个特定应用程序使用的针对每个节点的资源。

3. ApplicationMaster(AM)
ApplicationMaster 管理一个在 YARN 内运行的应用程序的每个实例。向 RM 申请 Container，RM 指定 NM 分配具体的 Container 给 AM，AM 将任务下发到容器并监控 Job 的运行状态、运行进度等

#### 程序执行流程
1. Client 提交任务给 ResourceManager，ResoureManager 下的 ApplicationManager 接收请求，分配第一个 Container 来运行ApplicationMaster，ApplicationManager 监控 ApplicationMaster 状态；

2. ApplicationMaster 向 ResourceManager 申请 Container，ResourceManager 下的 Scheduler 告知有哪些 Container 可用，并告知slaves 的 NodeManager 分配具体的 Container 给 ApplicationMaster；

3. ApplicationMaster 请求 slaves 下的 NodeManager 分配具体的 Container，ApplicationMaster 获得具体的 Container 执行任务，并跟踪监控该任务的全部过程（运行状态、运行进度等）；

4. NodeManager 监控 Container（CPU、内存）的使用情况，并告知 ResourceManager 下的 Scheduler 做好记录，以方便其他任务申请资源。