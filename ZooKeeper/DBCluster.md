#zbs  
`dbcluster` 析构的时候需要从 `quorum_cluster_` 获取 zk client 指针做一点操作。
dbcluster 什么时候析构？
MetaContext 析构的时候析构。
MetaContext 什么时候析构？
`MetaServer::StopService()` 析构，`MetaServer::StartService()` 生成

# QuorumCluster、QuorumServer、DBCluster 和 ZooKeeper Client 的关系

场景：断开网络。

## zk client

zk c-client context: class Zookeeper this
zk c-client +binds+ ZooKeeper::ZooWatcher。ZooWatcher 将事件入队 zk*event_queue*。
zk*event_queue* + ZooKeeper :: ZkEventHandler
ZkEventHandler：

- 如果是 session event，就调用 session watchers
  - Session watchers：`QuorumCluster::Watcher`
- 否则调用 watchers

ZOO_CONNECTED_STATE -> ZOO_CONNECTING_STATE
触发 `QuorumCluster::Watcher`

## QuorumCluster

触发事件： `QuorumClusterEvent::EXPIRED` -> status*watcher*

## QuorumServer

`QuorumClusterEvent::EXPIRED` -> `InthreadNotifyStop(EAgain);`
`InthreadNotifyStop` -> `quorum_cluster_.NotifyStop();`

## QuorumCluster

触发事件： `QuorumClusterEvent::STOPPED` -> status*watcher*

## QuorumServer

`StopService()`

- `StopDbCluster()`
  - 设置 `stopped_ = true`
- `delete meta context`
  - dbcluster 析构
    - `ZooKeeper* zk = quorum_cluster_->GetZk();`

## QuorumCluster

- 离开集群
- `ZooKeeper::Close`

## 线程

dbcluster : kDbClusterIOThreadName
meta sm: "system-manager"
quorum: "quorum"
