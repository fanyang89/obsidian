---
title: libelection 设计
---

# libelection 设计

思路：使用 Basic paxos 让所有的节点对 $X_{term}$ 达成一致，其中 $term$ 为任期，每发起一次新的选举会自增。

## 基本运行过程

- 每个节点以 `Running` 状态启动
- 外部服务发现心跳超时，通知 `libelection` 进入选举状态
- 进入选举状态。
  - Prepare：在一个选举超时时间 `election_timeout=random(100, 200)` 后，开始广播 `VoteProposeRequest`
  -
