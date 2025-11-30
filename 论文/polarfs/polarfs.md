---
marp: true
paginate: true
theme: default
footer: "PolarFS: An Ultra-low Latency and Failure Resilient Distributed File System"
math: mathjax
transition: push
style: |-
  .slide {
    font-family: '汉仪旗黑 50S' !important;
  }
  section.polarfs h1 {
    color: #102C57;
    opacity: 0.7;
  }
  section.raft h1 {
    color: #E2C799;
    opacity: 0.76;
  }
---

<!-- _class: polarfs -->

# <!--fit--> _PolarFS_

![bg](assets/polar.jpg)

_An Ultra-low Latency and Failure Resilient Distributed File System for Shared Storage Cloud Database_

_[fanyang@smartx.com](fanyang@smartx.com)_

---

# PolarFS 简介

PolarFS 专为 PolarDB 数据库服务设计，是一个**超低延迟**和**高可用**的分布式文件系统。

PolarFS 随机写延迟约 48us、Ext4 约 10us、CephFS 约 760us

- **PolarFS**：采用轻量级网络协议栈和用户空间 I/O 协议栈，充分利用了 RDMA、NVMe、SPDK 等新兴技术

- **ParallelRaft**：最大化 PolarFS I/O 吞吐
  - 打破了 Raft 严格的序列化限制，实现了高并发的共识协议

---

# 背景

- 当前的云平台难以充分利用 RDMA 和 NVMe SSD 等新兴硬件能力
- Raft 的机制在超低延迟硬件上，严重阻碍 PolarFS I/O 吞吐的上限
  - 实现了 ParallelRaft，允许乱序 ACK 和乱序 Commit
  - 结果：显著提高 PolarFS 并发性能

---

# PolarDB 架构

![bg right:33% width:440px](assets/1.png)

- Primary 可读写，Read only 节点只读
  - 主节点和只读节点通过 PolarFS，共享同个目录下的 redo log 和数据文件

- PolarFS 提供如下特性支持 PolarDB：
  - 同步文件元数据的修改（Truncate、expand、create、delete）
  - 文件元数据的一致性级别：Serializable
  - 对于网络分区：PolarFS 可以确保只有真正的主节点可以提供服务，防止数据损坏

---

# PolarFS 架构

![bg left:36% width:480px](assets/2.png)

PolarFS 主要由两层组成：

- 上层管理文件系统元数据并提供文件系统 API
  - 文件系统层支持卷中的文件管理，并负责文件系统元数据并发访问的互斥和同步
- 下层是存储管理
  - 存储层负责管理存储节点的所有磁盘资源，为每个数据库实例提供一个卷

---

# PolarFS 架构

![bg right:34% width:460px](assets/2.png)

- libpfs 提供类似 POSIX 的文件系统 API
- PolarSwitch 驻留在计算节点上，将 I/O 重定向到 ChunkServer 上
- ChunkServer 部署在存储节点上，提供 I/O 服务
- PolarCtrl 控制平面。包括一组微服务作为主控，和在计算与存储节点上部署的 agent
- 使用 MySQL 作为元数据存储

---

# 存储层

存储层给文件系统层提供管理卷和访问卷的接口。

- 卷由一组 chunk 组成。Chunk 是数据分布的最小单元。
- 卷的大小从 10G 到 100T 不等，可以扩容。
- 卷可以以 512B 对齐随机访问。
- 单个 I/O 请求中对同个 chunk 的修改是原子的

---

# Chunk

- 数据分配的最小单元
- 单个 Chunk 不会跨越多个磁盘
- 默认三副本
- Chunk 可以在 ChunkServer 中自动迁移，避免热点
- Chunk 大小是 10G
  - 降低元数据量，简化元数据管理
  - 缺点：粒度较大，无法进一步分离同个 Chunk 上的热点

---

# Block

- 一个 Chunk 分成若干个 Block
- 一个 Block 大小是 64KB
- Chunk LBA 与 Block 的映射表，以及每个空闲块的 Bitmap 都存储在 ChunkServer 本地
- 单个 Chunk 的映射表占用 640KB，可以放在 ChunkServer 内存中

---

# PolarSwitch

![bg left:33% width:400px](assets/2.png)

- 是部署在计算节点的 Daemon，它负责 I/O 请求映射到具体的后端节点
- 数据库通过 libpfs 将 I/O 请求发送给 PolarSwitch
  - 每个请求包含了数据库实例所在的 Volume ID、offset、length
- PolarSwitch 将其划分为对应的多个 Chunk，并将请求发往 Chunk 所属的 ChunkServer 完成访问

---

# ChunkServer

![bg right:33% width:400px](assets/2.png)

- ChunkServer 部署在后端存储节点上，一个存储节点可以有多个 ChunkServer
- 每个 ChunkServer 绑定一个核，管理一块 SSD
- ChunkServer 负责 Chunk 内的资源映射和读写。每个Chunk都包括一个WAL
  - ChunkServer 使用了 3D XPoint SSD 和普通 NVMe SSD 混合的 WAL
- ChunkServer 会复制写请求到对应的 Chunk 副本（其他 ChunkServer）上
  - 通过 ParallelRaft 保证 Chunk 副本间数据一致

---

# PolarCtrl

![bg right:33% width:400px](assets/2.png)

PolarCtrl 是集群的控制核心，使用 MySQL 云数据库存储元数据

- 监控 ChunkServer 的健康状况，确定哪些 ChunkServer 属于 PolarFS 集群
- Volume 创建及 Chunk 的分配、Volume 至 Chunk 的元数据信息维护
- 向 PolarSwitch 推送元数据缓存
- 监控 Volume 和 Chunk 的 I/O 性能
- 周期性地发起副本内和副本间的 CRC 数据校验

---

<!-- _class: raft -->

# <!--fit--> _RAFT_

![bg](assets/pete-raft.jpg)

---

# Raft 日志复制

- Leader 接收到请求，持久化到本地，发送 RPC 给 Follower 追加日志。多数 Follower 返回时，Leader 应用日志到状态机并返回结果给上层；Follower 下次心跳时，应用日志到状态机
- 某些节点具有 Leader 不具有的日志：Follower 覆盖这些没有提交的日志（但是 Leader 不能提交之前任期的日志）
- Follower 收到了一个 $prevLogIndex$ 和 $prevLogTerm$ 不匹配的日志：拒绝持久化，并且返回上一个日志号给 Leader；Leader 收到后，返回上一个日志；不断循环直到找到分岔点，然后继续复制过程

---

# Raft 领导人选举

- Leader 周期性的发送心跳给所有 Follower 维持自己的地位，阻止新的选举
- 一段随机超时后 Follower 没有收到心跳，自增自己的任期号，发起选举：推举自己为领导者候选，投票给自己，发送 RequestVote 给其他所有节点
- 收到超过半数选票的 Candidate 成为 Leader，立即发送心跳结束选举；Candidate 收到新 Leader 的心跳，确认其 $term$ 不小于自己，则成为 Follower
- 拥有更新的已提交日志的 Candidate 拒绝投票给拥有更旧日志的候选人

---

# Raft 安全性

![bg right:50% w:600px](assets/4.png)

- 新选举出的 Leader 必须包含所有**已提交**的日志：一个具有更新已提交日志的节点会拒绝一个具有更旧节点的投票请求
- Leader 只能提交当前任期的日志。对于之前任期的日志，只能进行复制，不能提交

---

# Raft 与高并发

设计之初，PolarFS 考虑到实现的复杂性，选择了 Raft。但：

- **Raft 日志不允许有日志空洞**：日志都是按序复制，按序应用
  - 不适合使用多个连接在 Leader 和 Follower 之间传输日志；当一个连接阻塞或者变慢时，日志条目有可能会乱序到达 Follower，但 Follower 必须按序接受日志条目；当大多数 Follower 被这些丢失的日志条目阻塞时，Leader 会出现问题
- **并发请求会被按序提交**
  - 经典 I/O 系统不保证 inflight 顺序，但 Raft 提供了更强的保证
  - 在一个请求之前的所有请求持久化之前，无法提交，降低了吞吐
  - 观察到当 iodepth 从 8 增加到 32 时，吞吐量下降了一半
- MultiRaft？
  - 没有真正解决此问题，划分 region 只是缓解影响

---

# ParallelRaft 对 Raft 的改造

**前提**：如果日志的写入范围互不重叠，则可以乱序执行（需要感知应用层信息）

- 日志持久化后 Follower 就可以立即返回 ACK，不需要等待前序日志
- 日志条目可以在大多数副本确认后立即提交
- 需要通过 look behind buffer 来判断是否可以应用
  - 如果有重叠，则需要等待空洞补齐
  - 如果没有重叠，则可以直接应用

---

# Look behind buffer

- 由于日志复制和提交可以乱序，**日志中会存在空洞**。

- 一个日志条目提交后，并不意味着在这个日志之前的所有日志都已经被成功提交

- 那么，如何安全的应用日志？

- 引入 look behind buffer：包含前 $N$ 个日志条目修改过的 LBA
  - 通常 $N$ 设置为 2
  - Follower 可以据此判断某个日志条目是否冲突
  - 无冲突则可应用
  - 否则加到待处理列表中，等到 Log 空洞补齐后才能应用

---

# Look behind buffer：例子

![lbb](assets/lbb.jpg)

---

# ParallelRaft 选举

- 选择具有最新 checkpoint 的节点作为 leader 候选，而不是拥有最新日志的节点

- 原因：
  - 日志合并阶段可以从其他节点获取到最新的日志
  - Checkpoint 里的日志都已经确认提交
  - 降低日志追赶（catch up）的代价

---

# 日志合并

![bg right:40% w:500px](assets/5.jpg)

由于日志空洞的存在，新 Leader 需要进行日志合并才能开始提供服务

- 选举阶段：拥有最新 checkpoint 的节点成为 Leader
- 日志合并：
  - Follower 发送自己在 checkpoint 之后的所有日志给 Leader (1)
  - Leader 进行日志合并，同步信息给 Follower (2)
  - Leader 提交 (3)，并通知 Follower 提交；Leader 开始服务

---

# 日志合并

<!-- footer: "" -->

对于已提交的日志：

- Leader 收到 Follower 已提交的日志，直接提交
- 对于不同节点上相同 $log index$ 但是不同 $term$ 的日志，选择 $term$ 更大的日志进行提交

对于未提交的日志：

- 宕机的节点被认为拥有这些未提交的日志
- 如果某个日志满足条件 (1)，则这个日志一定没有提交
  - 条件 (1)：$M_{拥有日志的节点数} + N_{宕机数} < P_{不包含日志的节点数}$
- 如果某个日志满足条件 (2)，则这个日志需要提交
  - 条件 (2)：$M_{拥有日志的节点数} + N_{宕机数} > P_{不包含日志的节点数}$
- 那么，代价是什么呢？

---

# 日志合并：例子

![w:800px](assets/logmerge.jpg)

---

# 追赶日志

追赶（Catch up）：让 Follower 跟上 Leader 当前的状态

- **快速追赶**：差异较小，进行增量同步
  - 通过 LBB 发现缺失的日志，并从 Leader 复制日志
- **流式追赶**：差异较大，进行全量同步
  - 使用 checkpoint 及其之后的所有日志，按照 128K 大小的粒度进行复制

---

# 经验教训

- 外部服务与内部可靠性：在背景流量和前台流量中取得平衡
- 中心化 vs 去中心化
  - GFS 和 HDFS 包含一个单一的主节点负责保存元数据和成员管理，这种系统的实施相对简单，但单一的主节点可能会成为整个系统的瓶颈
  - Dynamo 这样的去中心化系统则相反：节点之间是点对点的关系
  - PolarFS 做出了权衡：PolarCtrl 对于资源管理或者创建卷之类的管理任务是中心化的；ChunkServer 内部实现了自治：运行共识协议，自动处理数据恢复
- 等等...

---

# 评估

![bg right:60% w:800px](assets/praft-perf.png)

天下武功，唯快不破！

---

# 评估

![bg right:60% w:800px](assets/3.png)

天下武功，唯快不破！

---

<!-- _class: polarfs -->

# 感谢聆听！

![bg](assets/polar.jpg)
