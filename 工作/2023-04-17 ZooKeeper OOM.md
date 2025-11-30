# 概述

应该是之前创建了一个20T 的虚拟磁盘，格式化成 ext4，发现占用一般的空间，我发了一个格式化的时候不下发 writesame 的命令，他把之前的20T 删了，然后新建20T 的虚拟磁盘，再用新的命令格式化，然后就发现集群出问题了

# 出问题时间点

```bash
E0414 15:19:49.061784 22037 main.cc:82] [ZOO] handle_socket_error_msg:2495 2023-04-14 15:19:49,061:8789(0x7f071cac5700):ZOO_ERROR@handle_socket_error_msg@2495: Socket [192.168.6.13:2181] zk retcode=-4, errno=104(Connection reset by peer): failed while receiving a server response
```

ZooKeeper 停止工作：

```bash
# 163
2023-04-14T15:19:34,864 [myid:] - INFO  [SyncThread:3:FileTxnLog@266] - Creating new log file: log.3000d242c
2023-04-14T15:19:36,802 [myid:] - INFO  [SyncThread:3:FileTxnLog@266] - Creating new log file: log.3000d27f8
2023-04-14T15:19:39,152 [myid:] - INFO  [SyncThread:3:FileTxnLog@266] - Creating new log file: log.3000d2bc6
2023-04-14T15:19:41,916 [myid:] - INFO  [SyncThread:3:FileTxnLog@266] - Creating new log file: log.3000d2fa1
2023-04-14T15:19:52,137 [myid:] - INFO  [main:QuorumPeerConfig@137] - Reading configuration from: /etc/zookeeper/zoo.cfg                                                                                                                                                     2023-04-14T15:19:52,143 [myid:] - INFO  [main:QuorumPeerConfig@381] - clientPort is not set
```

Reclaim 间隔过长。怀疑在忙于 auto replay。
![](assets/Pasted%20image%2020230418114927.png)
