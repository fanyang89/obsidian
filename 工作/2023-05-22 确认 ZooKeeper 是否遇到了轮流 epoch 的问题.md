Node 1 损坏，只观察 Node 2 和 Node 3

```
# node3
2023-05-19T23:47:57,354 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):Leader@687] - Shutdown called
	java.lang.Exception: shutdown Leader! reason: Not sufficient followers synced, only synced with sids: [ [3] ]

# 这两行日志中间只有一个 if，但是却间隔了 0.5s
2023-05-19T23:47:56,885 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):Leader@681] - Shutting down
2023-05-19T23:47:57,354 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):Leader@687] - Shutdown called

2023-05-19T23:48:27,545 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):ZooKeeperServer@624] - shutting down

2023-05-19T23:48:27,825 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):SessionTrackerImpl@237] - Shutting down
# 长间隔
2023-05-19T23:48:43,874 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):LeaderRequestProcessor@77] - Shutting down
2023-05-19T23:49:06,739 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):SyncRequestProcessor@191] - Shutting down
# Processors shutdown 缓慢

2023-05-20T00:03:03,981 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.7.90.233:2181)(secure=disabled):ZKDatabase@254] - fastForwardFromEdits elapsed 837204ms
```

```
# node2
2023-05-19T23:49:58,026 [myid:] - INFO  [QuorumPeer[myid=2](plain=10.7.90.232:2181)(secure=disabled):FastLeaderElection@962] - Notification time out: 60000
```
