- https://issues.apache.org/jira/browse/ZOOKEEPER-335
- https://issues.apache.org/jira/browse/ZOOKEEPER-1549
  Superseded
  - https://issues.apache.org/jira/browse/ZOOKEEPER-1653
  - https://issues.apache.org/jira/browse/ZOOKEEPER-2354
    Newer version
  - https://issues.apache.org/jira/browse/ZOOKEEPER-1413
- https://issues.apache.org/jira/browse/ZOOKEEPER-4269

## 描述

Currently the zookeeper followers do not commit the new leader election. This will cause problems in a failure scenarios with a follower acking to the same leader txn id twice, which might be two different intermittent leaders and allowing them to propose two different txn's of the same zxid.

当前，follower 不会 commit 新的 leader election。如果一个 follower 两次 ACK 同一个 leader txn id，这两个 leader 可能会是两个不一样的 leader 并且允许这两个不同的 leader propose 两个拥有相同 zxid 的不同的 txn。

## 详细

在 Recovery 过程中

1. 两个 follower 重启
2. New leader epoch +1
3. Old leader 超时未到，没有进入 LOOKING 状态
4. 两个 Follower 与新 leader 断开连接，其中一个 follower 回到 Old leader 所在的集群
5. New leader 由于 follower 数量不足，返回 LOOKING 状态
6. 网络恢复后，new leader 作为 follower 加入 old leader 所在的集群
   1. New leader 发现自己的 epoch 比集群中的大，进入 LOOKING 状态
   2. 不断循环

## 网友

https://blog.csdn.net/hongge586/article/details/114581091
