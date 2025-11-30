```
I1108 20:51:02.315196 16992 meta_server.cc:404] Start the follower role.
I1108 20:51:02.316247 16992 meta_server.cc:348] Meta status change to new status: META_RUNNING meta addr: 10.0.0.49 port: 10100. Last status was: META_BOOTING which last for 2364 ms
I1108 20:51:02.320209 16992 meta_server.cc:411] Done starting the services of meta.
I1108 20:51:02.321202 16992 quorum_server.cc:59] Get quorum cluster event: Members_Changed
I1108 20:51:02.322194 16992 quorum_server.cc:78] Members changed.
I1108 20:51:02.316299 17071 thread.cc:114] Set thread name to: MetaRpcServerTh
I1108 20:51:02.327232 17071 coroutine.cc:372] Adjust max cached coroutines for current thread,  old: 64 new:64 current:0
I1108 20:51:02.332317 17071 meta_server.cc:292] Meta rpc server starts at 10.0.0.49:10100
W1108 20:56:32.489434 16994 main.cc:84] [ZOO] zookeeper_interest:2288 2022-11-08 20:56:32,489:16989(0x7feceed2e700):ZOO_WARN@zookeeper_interest@2288: Exceeded deadline by 145139ms
E1108 20:56:32.682780 16994 main.cc:81] [ZOO] handle_socket_error_msg:2390 2022-11-08 20:56:32,682:16989(0x7feceed2e700):ZOO_ERROR@handle_socket_error_msg@2390: Socket [10.0.0.52:2181] zk retcode=-7, errno=110(Connection timed out): connection to 10.0.0.52:2181 timed out (exceeded timeout by 143139ms)
W1108 20:56:32.682852 16994 main.cc:84] [ZOO] zookeeper_interest:2288 2022-11-08 20:56:32,682:16989(0x7feceed2e700):ZOO_WARN@zookeeper_interest@2288: Exceeded deadline by 145332ms
I1108 20:56:32.683390 16994 main.cc:89] [ZOO] check_events:2477 2022-11-08 20:56:32,683:16989(0x7feceed2e700):ZOO_INFO@check_events@2477: initiated connection to server [10.0.0.49:2181]
E1108 20:56:32.698276 16994 main.cc:81] [ZOO] handle_socket_error_msg:2495 2022-11-08 20:56:32,698:16989(0x7feceed2e700):ZOO_ERROR@handle_socket_error_msg@2495: Socket [10.0.0.49:2181] zk retcode=-4, errno=112(Host is down): failed while receiving a server response
E1108 20:56:32.724102 16992 db_cluster.cc:406] Error occurred when doing auto replay:
Traceback:
[EZKConnectError]:  [GET CHILDREN] path: /zbs/meta/__mj_j
I1108 20:56:32.724176 16998 zookeeper.cc:727] Start closing the zookeeper session.
I1108 20:56:32.724282 16994 main.cc:89] [ZOO] check_events:2477 2022-11-08 20:56:32,724:16989(0x7feceed2e700):ZOO_INFO@check_events@2477: initiated connection to server [10.
0.0.53:2181]
I1108 20:56:32.725104 16998 main.cc:89] [ZOO] zookeeper_close:3357 2022-11-08 20:56:32,725:16989(0x7feced52b700):ZOO_INFO@zookeeper_close@3357: Freeing zookeeper resources f
or sessionId=0x201571266490e3b
I1108 20:56:32.725175 16998 zookeeper.cc:734] Finish closing the zookeeper session.
I1108 20:56:32.725198 16998 zookeeper.cc:737] zookeeper closed.
```
