```
#6  0x000055857a7d5ca9 in google::LogMessageFatal::~LogMessageFatal (this=<optimized out>, __in_chrg=<optimized out>) at glog/src/logging.cc:2023
#7  0x000055857a51f95d in Unlock (this=<optimized out>) at src/common/locks.h:40
#8  ~GenericLockGuard (this=<optimized out>, __in_chrg=<optimized out>) at src/common/locks.h:60
#9  CleanQueue (clean_func=..., this=<optimized out>) at src/common/async_queue.h:121
#10 CleanQueue (clean_func=..., this=<optimized out>) at src/common/async_queue.h:280
#11 zbs::consensus::ZooKeeper::~ZooKeeper (this=0x55857cfa0030, __in_chrg=<optimized out>) at src/consensus/zookeeper.cc:140
#12 0x000055857a514862 in zbs::consensus::QuorumCluster::~QuorumCluster (this=0x55857cfa0020, __in_chrg=<optimized out>) at src/consensus/quorum_cluster.cc:131
#13 0x000055857a4ae10f in ~QuorumServer (this=0x55857cfa0000, __in_chrg=<optimized out>) at src/consensus/quorum_server.h:40
#14 zbs::task::TaskServer::~TaskServer (this=0x55857cfa0000, __in_chrg=<optimized out>) at src/task/task_server.cc:67
#15 0x000055857a4ae261 in zbs::task::TaskServer::~TaskServer (this=0x55857cfa0000, __in_chrg=<optimized out>) at src/task/task_server.cc:71
#16 0x000055857a47bb6c in main (argc=<optimized out>, argv=<optimized out>) at src/task/task_main.cc:87
```

日志时间线：

```
// 断开连接
W1202 01:45:06.097442 59089 main.cc:84] [ZOO] zookeeper_interest:2288 2022-12-02 01:45:06,097:16090(0x7f2f40169700):ZOO_WARN@zookeeper_interest@2288: Exceeded deadline by 2689ms
W1202 01:45:06.097581 59091 zookeeper.cc:217] zookeeper session event: CONNECTED_STATE --> CONNECTING_STATE
W1202 01:45:07.600075 59091 zookeeper.cc:180] Start the session timeout timer: 200
I1202 01:45:07.652040 59091 quorum_cluster.cc:485] QuorumCluster state change: Running --> Running_Connecting

// 重连，发现 session expired
I1202 01:45:07.464505 59089 main.cc:89] [ZOO] check_events:2477 2022-12-02 01:45:07,464:16090(0x7f2f40169700):ZOO_INFO@check_events@2477: initiated connection to server [10.136.168.64:2181]
W1202 01:45:07.652140 59089 main.cc:84] [ZOO] zookeeper_interest:2288 2022-12-02 01:45:07,652:16090(0x7f2f40169700):ZOO_WARN@zookeeper_interest@2288: Exceeded deadline by 221ms
E1202 01:45:07.652448 59089 main.cc:81] [ZOO] handle_socket_error_msg:2514 2022-12-02 01:45:07,652:16090(0x7f2f40169700):ZOO_ERROR@handle_socket_error_msg@2514: Socket [10.136.168.64:2181] zk retcode=-112, errno=116(Stale file handle): sessionId=0x100026e28c9019c has expired.
// session expired，停止服务
W1202 01:45:07.652709 59091 zookeeper.cc:217] zookeeper session event: CONNECTING_STATE --> EXPIRED_SESSION_STATE
E1202 01:45:07.652730 59091 quorum_cluster.cc:384] Zookeeper session timeout.
I1202 01:45:07.652740 59091 quorum_cluster.cc:485] QuorumCluster state change: Running_Connecting --> Expired
I1202 01:45:07.652781 59091 quorum_server.cc:76] Get quorum cluster event: EXPIRED
W1202 01:45:07.652791 59091 quorum_server.cc:91] Session expired.
E1202 01:45:07.652807 59091 task_server.cc:348] Session lost, must exit to prevent the tasks from running in this task server.
I1202 01:45:07.652827 59091 quorum_server.cc:53] Quorum server begins stopping, this: 0x55857cfa0000
I1202 01:45:07.652839 59091 quorum_cluster.cc:485] QuorumCluster state change: Expired --> Stopped
I1202 01:45:07.652848 59091 quorum_server.cc:58] Quorum server stopped, this: 0x55857cfa0000
I1202 01:45:07.652858 59091 quorum_server.cc:76] Get quorum cluster event: STOPPED
I1202 01:45:07.652878 59091 task_server.cc:202] Stop runner
I1202 01:45:07.671368 59091 task_server.cc:313] Stop VIP Server

I1202 01:45:07.690935 59109 task_db.cc:346] Initializing LocalVIPDb
I1202 01:45:07.691185 59109 leveldb_backend.h:298] Close leveldb /var/lib/zbs/taskd/db//task_vip/vip
I1202 01:45:07.692327 59091 quorum_cluster.cc:315] QuorumCluster thread start closing.
// QuorumCluster 离开集群
I1202 01:45:07.692373 59091 quorum_cluster.cc:681] Leave cluster 10.136.168.65:10601

// zk.Close()
I1202 01:45:07.692413 59091 zookeeper.cc:727] Start closing the zookeeper session.
I1202 01:45:07.692529 59091 main.cc:89] [ZOO] zookeeper_close:3357 2022-12-02 01:45:07,692:16090(0x7f2f3f167700):ZOO_INFO@zookeeper_close@3357: Freeing zookeeper resources for sessionId=0x100026e28c9019c
I1202 01:45:07.692585 59091 zookeeper.cc:734] Finish closing the zookeeper session.
I1202 01:45:07.692596 59091 zookeeper.cc:737] zookeeper closed.

// QuorumServer 析构
I1202 01:45:07.801932 16090 quorum_server.h:40] ~QuorumServer, this: 0x55857cfa0000
// zk_ 成员析构
I1202 01:45:07.819953 16090 zookeeper.cc:132] Destructing zookeeper.
// zk.Close() 已经调用过
I1202 01:45:07.851056 16090 zookeeper.cc:737] zookeeper closed.
W1202 01:45:07.874543 16090 zookeeper.cc:196] Stop the session timeout timer.
W1202 01:45:07.874625 16090 event_loop.cc:168] del non-existing event for fd 32
W1202 01:45:07.874641 16090 event_loop.cc:176]  [SYSCALL]: ::epoll_ctl(efd_, EPOLL_CTL_DEL, fd, nullptr). [ARG LIST]: fd: 32 .[SYSMSG]: No such file or directory [2].
I1202 01:45:07.874682 16090 zookeeper.cc:142] Done destructing zookeeper.
// zk 析构完成

// zk.StopTimer 再次被调用
W1202 01:45:08.027252 59091 zookeeper.cc:196] Stop the session timeout timer.
F1202 01:45:08.027406 16090 locks.h:22] Check failed: ret == 0 || ret == ETIMEDOUT pthread_mutex_destroy(&mutex_) for: Device or resource busy
```
