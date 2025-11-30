```
W0926 14:25:24.334420 28801 db_cluster.cc:1091] [SLOW ZK COMMIT] takes 4 s. retried 0 times
I0926 14:25:24.335896 28801 db_cluster.cc:247] Try to open/create db c2
E0926 14:25:31.067209 28738 main.cc:82] [ZOO] handle_socket_error_msg:2495 2022-09-26 14:25:31,067:28713(0x7fc42906b700):ZOO_ERROR@handle_socket_error_msg@2495: Socket [112.16.18.46:2181] zk retcode=-4, errno=112(Host is down): failed while receiving a server response
W0926 14:25:31.067353 28738 main.cc:85] [ZOO] zookeeper_interest:2288 2022-09-26 14:25:31,067:28713(0x7fc42906b700):ZOO_WARN@zookeeper_interest@2288: Exceeded deadline by 2665ms
I0926 14:25:31.150213 28738 main.cc:90] [ZOO] check_events:2477 2022-09-26 14:25:31,150:28713(0x7fc42906b700):ZOO_INFO@check_events@2477: initiated connection to server [112.16.18.44:2181]
E0926 14:25:31.215224 28738 main.cc:82] [ZOO] handle_socket_error_msg:2514 2022-09-26 14:25:31,215:28713(0x7fc42906b700):ZOO_ERROR@handle_socket_error_msg@2514: Socket [112.16.18.44:2181] zk retcode=-112, errno=116(Stale file handle): sessionId=0x3000012beb10001 has expired.
E0926 14:25:31.215440 28735 quorum_cluster.cc:462] Update members failed:
Traceback:
[EZKConnectError]:  [GET CHILDREN] path: /zos/service/meta
W0926 14:25:31.215490 28735 zookeeper.cc:220] zookeeper session event: CONNECTED_STATE --> CONNECTING_STATE
W0926 14:25:31.215512 28735 zookeeper.cc:183] Start the session timeout timer: 3600
I0926 14:25:31.215543 28735 quorum_cluster.cc:443] QuorumCluster state change: Running --> Running_Connecting
W0926 14:25:31.215564 28735 zookeeper.cc:220] zookeeper session event: CONNECTING_STATE --> EXPIRED_SESSION_STATE
E0926 14:25:31.215585 28735 quorum_cluster.cc:349] Zookeeper session timeout.
I0926 14:25:31.215603 28735 quorum_cluster.cc:443] QuorumCluster state change: Running_Connecting --> Expired
W0926 14:25:34.815616 28735 zookeeper.cc:199] Stop the session timeout timer.
W0926 14:25:34.815896 28735 zookeeper.cc:220] zookeeper session event: EXPIRED_SESSION_STATE --> EXPIRED_SESSION_STATE
```
