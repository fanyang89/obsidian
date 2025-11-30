---
title: 实现精细的 ZooKeeper Recovery 超时控制
author: fanyang
pubDatetime: 2024-08-14T14:00:00Z
slug: zookeeper-init-limit-phased
featured: false
draft: true
tags:
  - zookeeper
  - consensus
description: >
  ZooKeeper 使用 initLimit 控制 Recovery 超时，导致了在数据量大的情况下 discovery 超时慢降低系统可用性的问题。本文提出了一种简单的办法，将超时分割为两个阶段解决了这个问题。
---

## ZooKeeper Discovery Phase 简介

本文讨论的是协议中的 Discovery 阶段。
![[1e85b012115ac5b757ae3e303d5f29d612d4cf53a3271b8fe14ca47a252b99f22268a831fdb8c43d1cdd5ff6d47490d698c4f020d5bff4b5581a7ca287f097ce.png|500]]
这是 `braft` 文档中对 [Zab 协议](https://marcoserafini.github.io/assets/pdf/zab.pdf)的配图：
![zab_recovery_phase](zab_recovery_phase.png)
从图中可以看到，在选举结束之后，Follower 需要向 Leader 发送 `FOLLOWERINFO` 消息，通报自己的身份。Leader 在收到多数 Follower 发送的 epoch 后，发送 `LEADERINFO` 通报新纪元（new epoch，上一步骤中获取的 `epoch + 1`）；等待多数 Follower 响应 `ACK` 之后，发起数据同步过程。
代码中的 Leader 实现位于 `org/apache/zookeeper/server/quorum/Leader.java`。

```java
self.setZabState(QuorumPeer.ZabState.DISCOVERY);
self.tick.set(0);
zk.loadData();

leaderStateSummary = new StateSummary(self.getCurrentEpoch(), zk.getLastProcessedZxid());

// Start thread that waits for connection requests from
// new followers.
cnxAcceptor = new LearnerCnxAcceptor();
cnxAcceptor.start();

long epoch = getEpochToPropose(self.getId(), self.getAcceptedEpoch());
zk.setZxid(ZxidUtils.makeZxid(epoch, 0));

synchronized(this){
    lastProposed = zk.getZxid();
}

newLeaderProposal.packet = new QuorumPacket(NEWLEADER, zk.getZxid(), null, null);
```

代码中，`LEADERINFO` 对应 `NEWLEADER` 消息。在 `getEpochToPropose` 函数中可以看到接收超时是 `self.getInitLimit() * self.getTickTime()`，也就是配置文件中 `initLimit` 对应的时间。
Follower 实现位于 `org/apache/zookeeper/server/quorum/Follower.java`

```java
self.setZabState(QuorumPeer.ZabState.DISCOVERY);
QuorumServer leaderServer = findLeader();
try {
    connectToLeader(leaderServer.addr, leaderServer.hostname);
    long newEpochZxid = registerWithLeader(Leader.FOLLOWERINFO);
```

选举完成后，Follower 会向 Leader 发送 `FOLLOWERINFO`，包体内容是 accepted epoch。

## 故障场景

Discovery 和 recovery 的两个过程，超时都是通过 `initLimit` 进行控制。当需要同步的数据量很多的时候，如新增节点需要进行 `SNAP` 全量的数据同步，这个过程可能耗时很长，因此 `initLimit` 不宜设置过小。
但是这也带来了新的问题，考虑下面的场景：某节点配置的 `dataDir` 无法正常读写，此时仍然能正常响应选举投票。虽然节点从正常运行状态退出，但选举时仍然可能是故障节点跟某正常节点成为集群。由于节点故障，在获取 epoch 过程中无法完成 I/O，导致正常节点 `getEpochToPropose` 时超时（`initLimit`）退出，重新回到选举阶段。对于网络故障下也是同理，Leader 可能无法在 `initLimit` 时间内正常获取到 Follower epoch。
这通常来说并不是什么大问题，ZooKeeper 会自动重新选举，并最终回到自愈恢复正常工作。但是当 `initLimit` 设置的比较大时，就会导致不可用的时间很长。

## 如何改进

这里可以很容易的看出来，`initLimit` 一个参数，混杂了两个不同的控制

- Epoch 协商超时的控制
- 数据同步超时的控制
  这两方面对于超时的考虑是不同的。那么，只需要为 epoch 协商增加一组参数，是不是就可以解决问题了？

## 安全性

假设我们新增加的参数名为 $epochLimit$，并且已经构建了一个版本。新版本使用较低的 $epochLimit'$，旧版本使用较大的 $epochLimit$，已知

$$
epochLimit' < epochLimit
$$

考虑一个典型的三节点集群，其中一节点宕机，一节点新版本，一节点旧版本。
