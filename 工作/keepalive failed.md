```
$ls -lah
total 8.0K
drwxr-xr-x.  2 root root 4.0K Aug 15 14:20 .
drwxr-xr-x. 21 root root 4.0K Aug 14 17:12 ..
-rw-rw-rw-   1 root root    0 Aug 15 14:20 lsm.core.28365.1660544405.gz
-rw-rw-rw-   1 root root    0 Aug 15 14:20 lsm.core.28464.1660544408.gz
-rw-rw-rw-   1 root root    0 Aug 15 14:20 lsm.core.28545.1660544412.gz
-rw-rw-rw-   1 root root    0 Aug 15 14:20 lsm.core.28610.1660544415.gz
-rw-rw-rw-   1 root root    0 Aug 15 14:20 lsm.core.28686.1660544418.gz
```

```
I0815 14:19:06.200080 24365 recover_handler.cc:712] [NORMAL RECOVER START] pid: 20 state: START cur_block: 4294967295 src_cid: 3 dst_cid: 4 is_migrate: false silence_ms: 0 replace_cid: 0 epoch: 36 gen: 5651
I0815 14:19:06.200122 24365 recover_handler.cc:285] Get recover notification: pid: 2495 lease { owner { uuid: "6d7ede8b-f452-4cdc-aee7-f75b8a14e4fd" ip: "10.3.18.78" num_ip: 1309803274 port: 10201 cid: 3 secondary_data_ip: "192.168.18.78" zone: "default" scvm_mode_host_data_ip: "" alive_sec: 547 } pid: 2495 location: 259 origin_pid: 0 epoch: 2627 origin_epoch: 0 ever_exist: true meta_generation: 78 expected_replica_num: 3 chunks { id: 3 data_ip: 130980327W0815 14:19:24.357980 24385 proto_async_client.h:484] EPOLLRDHUP received.
W0815 14:19:24.372704 24385 proto_async_client.h:496] Failed to recv msg: [ENIOError]
W0815 14:19:24.374315 24385 session_follower.cc:287] [SESSION KEEPALIVE FAILURE]:  response: , st:
Traceback:
[EProtoAsyncClient]: closed
I0815 14:19:24.374378 24385 session_follower.cc:233] [FOLLOWER SESSION SLEEP BEFORE RECONNECT] sleep 1000 ms
W0815 14:19:24.359284 24365 proto_async_client.h:484] EPOLLRDHUP received.
W0815 14:19:24.374428 24365 proto_async_client.h:496] Failed to recv msg: [ENIOError]
```

```
// node1
W0815 14:19:24.358510  5067 zookeeper.cc:220] zookeeper session event: CONNECTED_STATE --> CONNECTING_STATE
W0815 14:19:25.739512  5067 zookeeper.cc:220] zookeeper session event: CONNECTING_STATE --> CONNECTED_STATE

I0815 14:19:25.776284  5067 quorum_cluster.cc:529] Leader is 10.3.18.78:10100. seq is 21
```

```
[root@node3 15:35:31 zbs]$zbs-tool service list
Name    Members                                                                           Leader            Specified members                                             Priority
------  --------------------------------------------------------------------------------  ----------------  ------------------------------------------------------------  ----------
meta    ['10.3.18.77:10100', '10.3.18.78:10100', '10.3.18.76:10100']                      10.3.18.76:10100  ['10.3.18.76:10100', '10.3.18.77:10100', '10.3.18.78:10100']
ntp     ['10.3.18.77:0', '10.3.18.78:0', '10.3.18.79:0', '10.3.18.76:0']                  10.3.18.78:0      ['']
taskd   ['10.3.18.77:10601', '10.3.18.78:10601', '10.3.18.76:10601', '10.3.18.79:10601']  10.3.18.79:10601  ['']
[root@node3 15:35:49 zbs]$cat /etc/zbs/zbs.conf
[network]
data_ip=10.3.18.78
heartbeat_ip=10.3.18.78
vm_ip=192.168.18.78
web_ip=192.168.18.78

[cluster]
role=master
members=10.3.18.76,10.3.18.77,10.3.18.78
zookeeper=10.3.18.76:2181,10.3.18.77:2181,10.3.18.78:2181
mongo=10.3.18.76:27017,10.3.18.77:27017,10.3.18.78:27017
cluster_mgt_ips=192.168.18.76,192.168.18.77,192.168.18.78,192.168.18.79
cluster_storage_ips=10.3.18.76,10.3.18.77,10.3.18.78,10.3.18.79
```
