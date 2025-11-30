![[assets/Pasted image 20220303101905.png]]

Node203

```
I0228 10:06:12.161804 49442 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 62
// ...
E0228 10:11:46.296921 49426 main.cc:400] Signal 15 was received
// ...
I0228 10:14:29.055189  5071 meta_main.cc:58] [LOOP RUNNING]
```

Node201

```
I0224 20:20:22.126979 25604 db_cluster.cc:247] Try to open/create db ss
// 时间跳变
I0228 10:06:12.177661 25604 quorum_cluster.cc:698] Ger priority config:

I0228 10:11:57.684108 25588 meta_main.cc:77] [META SERVER EXITS]: 0
// 中间的 2 分钟干嘛去了？
I0228 10:14:29.697149  4794 meta_main.cc:58] [LOOP RUNNING]
I0228 10:14:32.771126 4798 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 65
// ...
I0228 18:38:23.914621 4989 nfs_server.cc:1131] [SETATTR]: [REQUEST]: id: "d7fcf06c-43ca-4bdc" sattr3 { atime_how: SET_TO_SERVER_TIME atime { seconds: 0 nseconds: 0 } mtime_how: SET_TO_SERVER_TIME mtime { seconds: 0 nseconds: 0 } }, [RESPONSE]: ST:, name: "874e38bc-91e7-11ec-b36f-785773001d22" pool_id: "cd5839b4-9d3e-4649-8499-2feba5ed799f" id: "d7fcf06c-43ca-4bdc" parent_id: "4113a06a-5dcc-405b" attr { type: FILE mode: 493 uid: 0 gid: 0 size: 47 ctime { seconds: 1646044703 nseconds: 913866428 } mtime { seconds: 1646044703 nseconds: 913866428 } atime { seconds: 1646044703 nseconds: 913866428 } } volume_id: "d7fcf06c-43ca-4bdc-bc34-c5dbb2675cc7" xid: 3373427672, [TIME]: 776 us.
// 节点重启
```

```
[root@node201 14:14:43 ~]$uptime -s
2022-02-28 19:02:05
```

## 为什么 meta proxy 没有更新 leader？

时间点：

```
# Node201 meta
I0301 19:25:28.904667  6388 iscsi_server.cc:2023] [GET ISCSI LUN]: [REQUEST]: pool_path { pool_name: "zbs-iscsi-datastore-1645406080682v" } lun_id: 76, [RESPONSE]: ST:
Traceback:
[ENotLeader]:
, , [TIME]: 7 us.
```

Node203 chunk

```
I0301 19:25:26.489780  5072 socket_client_transport.cc:56] Close() is scheduled due to event handler running
W0301 19:25:26.490845  5072 data_channel_manager.cc:207] Fail to ping : 192.168.18.201:10201 ping error times: 3981 st:
Traceback:
[ESocketClosed]: client closed
I0301 19:25:26.490864  5072 socket_client_transport.cc:56] Close() is scheduled due to event handler running
I0301 19:25:27.096956  5072 access_handler.cc:832] [Update Volume]: 942d5035-7a33-444b-979b-f4105ef7e306
I0301 19:25:28.904275  5072 proto_async_client.h:169] Connect to 0.0.0.0:0-->40.0.18.201:10100 with timeout_ms 1000
I0301 19:25:28.904491  5072 proto_async_client.h:355] Connected to 40.0.18.203:37740-->40.0.18.201:10100
```

```bash
$ rg 'meta_proxy'
zbs-chunkd.log.20220224-203505.8882
7936:I0228 10:09:00.787704  8882 meta_proxy.cc:40] Found leader from zk: 40.0.18.203:10100

zbs-chunkd.log.20220228-101429.5072
720:I0228 10:14:56.698813  5072 meta_proxy.cc:40] Found leader from zk: 40.0.18.201:10100
283514:I0301 19:25:30.167500  5072 meta_proxy.cc:40] Found leader from zk: 40.0.18.203:10100
```

## zk 重启

202

```
2022-02-28T10:11:30,263 [myid:] - INFO  [CommitProcWorkThread-1:LearnerSessionTracker@121] - Committing global session 0x3001064848c7ecc
2022-02-28T10:14:25,664 [myid:] - INFO  [main:QuorumPeerConfig@136] - Reading configuration from: /etc/zookeeper/zoo.cfg
```

203

```
2022-02-28T10:11:46,322 [myid:] - WARN  [NIOWorkerThread-2:NIOServerCnxn@366] - Unable to read additional data from client sessionid 0x3001064848c003b, likely client has closed socket
2022-02-28T10:14:28,256 [myid:] - INFO  [main:QuorumPeerConfig@136] - Reading configuration from: /etc/zookeeper/zoo.cfg
```

201

```
2022-02-28T10:11:56,182 [myid:] - WARN  [NIOWorkerThread-1:NIOServerCnxn@370] - Exception causing close of session 0x0: ZooKeeperServer not running
2022-02-28T10:14:28,418 [myid:] - INFO  [main:QuorumPeerConfig@136] - Reading configuration from: /etc/zookeeper/zoo.cfg
```
