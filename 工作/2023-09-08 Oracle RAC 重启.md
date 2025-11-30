问题上报时间：2023-08-19 11:37
问题描述：SMTXOS 4.0.10 -> 5.0.6，升级过程中，虚拟机 oracle 服务停止
开始升级时间：`2023-08-19 10:50:35`

rac02 运行在 SCVM04 上，rac01 运行在 SCVM05上

```bash
2023-08-19 10:05:37.855:
[/u01/app/11.2.0/crs_1/bin/scriptagent.bin(15071)]CRS-5822:Agent '/u01/app/11.2.0/crs_1/bin/scriptagent_grid' disconnected from server. Details at (:CRSAGF00117:) {0:15:26} in /u01/app/11.2.0/crs_1/log/yzrac02/agent/crsd/scriptagent_grid/scriptagent_grid.log.
2023-08-19 10:05:37.862:
[/u01/app/11.2.0/crs_1/bin/orarootagent.bin(13461)]CRS-5822:Agent '/u01/app/11.2.0/crs_1/bin/orarootagent_root' disconnected from server. Details at (:CRSAGF00117:) {0:3:29998} in /u01/app/11.2.0/crs_1/log/yzrac02/agent/crsd/orarootagent_root/orarootagent_root.log.
2023-08-19 10:05:37.872:
[/u01/app/11.2.0/crs_1/bin/oraagent.bin(13457)]CRS-5822:Agent '/u01/app/11.2.0/crs_1/bin/oraagent_grid' disconnected from server. Details at (:CRSAGF00117:) {0:1:147} in /u01/app/11.2.0/crs_1/log/yzrac02/agent/crsd/oraagent_grid/oraagent_grid.log.
2023-08-19 10:05:37.872:
[/u01/app/11.2.0/crs_1/bin/oraagent.bin(5073)]CRS-5822:Agent '/u01/app/11.2.0/crs_1/bin/oraagent_oracle' disconnected from server. Details at (:CRSAGF00117:) {0:13:46591} in /u01/app/11.2.0/crs_1/log/yzrac02/agent/crsd/oraagent_oracle/oraagent_oracle.log.
2023-08-19 10:05:38.002:
[ohasd(3390)]CRS-2765:Resource 'ora.crsd' has failed on server 'yzrac02'.
2023-08-19 10:05:39.666:
[crsd(22553)]CRS-1013:The OCR location in an ASM disk group is inaccessible. Details in /u01/app/11.2.0/crs_1/log/yzrac02/crsd/crsd.log.

[ohasd(3390)]CRS-2765:Resource 'ora.crsd' has failed on server 'yzrac02'.
2023-08-19 10:05:58.533:
[ohasd(3390)]CRS-2771:Maximum restart attempts reached for resource 'ora.crsd'; will not restart.
2023-08-19 10:05:58.662:
[ohasd(3390)]CRS-2769:Unable to failover resource 'ora.crsd'.
```

节点 `10_39_4_11` 获取 NFS 权限失败：

```bash
I0819 10:04:57.974704  2393 data_channel_server_v2.cc:105] [DATACHANNEL SERVER] lost server_transport 0x564db4ea6d80 ServerTransport with peer TCP Socket (88.88.88.5:44138) via local TCP Socket (0.0.0.0:10201)
I0819 10:05:00.121261  2393 transport.cc:73] [NFS] accept new client on: 0.0.0.0:2049 fd: 14
I0819 10:05:02.798220  2393 generation_syncor.cc:121] [SYNC GENERATION START] pid: 467733 loc: [4 5 ]
W0819 10:05:02.809283  2393 proto_async_client.h:482] EPOLLRDHUP received.
W0819 10:05:02.809312  2393 proto_async_client.h:494] Failed to recv msg: [ENIOError]
W0819 10:05:02.831319  2393 proto_async_client.h:482] EPOLLRDHUP received.
W0819 10:05:02.831331  2393 proto_async_client.h:494] Failed to recv msg: [ENIOError]
W0819 10:05:02.912554  2480 proto_async_client.h:482] EPOLLRDHUP received.
W0819 10:05:02.912626  2480 proto_async_client.h:494] Failed to recv msg: [ENIOError]
W0819 10:05:02.912652  2480 proto_async_client.h:482] EPOLLRDHUP received.
W0819 10:05:02.912659  2480 proto_async_client.h:494] Failed to recv msg: [ENIOError]
W0819 10:05:02.912693  2480 session_follower.cc:288] [SESSION KEEPALIVE FAILURE]:  response: , st:
Traceback:
[EProtoAsyncClient]: closed
```

持续 14s

```bash
# 10_39_4_12/zbs_logs/zbs-metad/zbs-metad.log.20230819-092811.2376
I0819 09:48:43.536979  2414 meta_server.cc:400] Start the leader role

# exit at
I0819 10:05:02.808823  2414 quorum_server.cc:39] Stop quorum server:
Traceback: [EAgain]

# 10_39_4_13/zbs_logs/zbs-metad/zbs-metad.log.20230819-093855.2325
I0819 10:05:07.099628  2423 meta_server.cc:400] Start the leader role
```

```
# 10_39_4_13/zbs_logs/zbs-metad/zbs-metad.log.20230819-093855.2325
I0819 10:05:02.397724  2423 quorum_cluster.cc:443] QuorumCluster state change: Running_Connecting --> Expired
I0819 10:05:02.397754  2423 quorum_server.cc:59] Get quorum cluster event: EXPIRED
W0819 10:05:02.397761  2423 quorum_server.cc:74] Session expired.
I0819 10:05:02.397768  2423 quorum_server.cc:39] Stop quorum server:
Traceback: [EAgain]

I0819 10:05:03.677630 2423 meta_server.cc:404] Start the follower role.

I0819 10:05:06.431744 2423 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now. seq is 127
```

![](assets/Pasted%20image%2020230908130550.png)

```bash
[10_39_4_14]/zookeeper.log│2023-08-19T10:04:58,762 [myid:] - WARN  [QuorumPeer[myid=4](plain=88.88.88.4:2181)(secure=disabled):Follower@100] - Exception when following the leader

2023-08-19T09:48:36,643 [myid:] - INFO  [QuorumPeer[myid=5](plain=88.88.88.5:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: leading - broadcast
[10_39_4_15]/2023-08-19T10:04:48,326 [myid:] - INFO  [NIOWorkerThread-1:QuorumZooKeeperServer@157] - Submitting global closeSession request for session 0x501f6e279920054

[10_39_4_14]/zookeeper.log│2023-08-19T10:04:58,762 [myid:] - WARN  [QuorumPeer[myid=4](plain=88.88.88.4:2181)(secure=disabled):Follower@100] - Exception when following the leader
[10_39_4_14]/zookeeper.log│java.net.SocketTimeoutException: Read timed out
[10_39_4_14]/zookeeper.log│2023-08-19T10:04:59,243 [myid:] - INFO  [QuorumPeer[myid=4](plain=88.88.88.4:2181)(secure=disabled):QuorumPeer@1357] - LEADING
2023-08-19T10:05:00,279 [myid:] - INFO [QuorumPeer[myid=4](plain=88.88.88.4:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: leading - broadcast
2023-08-19T10:05:04,426 [myid:] - INFO [SessionTracker:ZooKeeperServer@447] - Expiring session 0x10000794e995091, timeout of 4000ms exceeded
```
