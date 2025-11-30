```
2022/08/04 20:15:00.456073 running cmd: 'ifdown ens224'
2022/08/04 20:15:00.514593 quorum matrix: [L X]
2022/08/04 20:15:00.883173 more than 1 leader appear at this tick, cnt=2
2022/08/04 20:15:00.883250 quorum matrix: [L L]
2022/08/04 20:15:01.049143 failed to get meta summary for meta 0, connection is shut down
2022/08/04 20:15:01.160537 quorum matrix: [X L]
2022/08/04 20:15:01.163138 sleep for 1.0s
2022/08/04 20:15:01.346164 failed to get meta summary for meta 0, connection is shut down
2022/08/04 20:15:01.497170 quorum matrix: [X L]
2022/08/04 20:15:02.166984 running cmd: 'ifup ens224'
```

```bash
# node2

# 网络中断
E0804 21:23:30.942003 31378 main.cc:82] [ZOO] handle_socket_error_msg:2390 2022-08-04 21:23:30,941:22188(0x7fcf97528700):ZOO_ERROR@handle_socket_error_msg@2390: Socket [172.16.91.1:2181] zk retcode=-7, errno=110(
Connection timed out): connection to 172.16.91.1:2181 timed out (exceeded timeout by 2ms)
W0804 21:23:30.942158 22200 zookeeper.cc:220] zookeeper session event: CONNECTED_STATE --> CONNECTING_STATE
W0804 21:23:30.942174 22200 zookeeper.cc:183] Start the session timeout timer: 3600
I0804 21:23:30.942200 22200 quorum_cluster.cc:443] QuorumCluster state change: Running --> Running_Connecting

# expired
W0804 21:23:34.542235 22200 zookeeper.cc:199] Stop the session timeout timer.
W0804 21:23:34.542359 22200 zookeeper.cc:220] zookeeper session event: CONNECTING_STATE --> EXPIRED_SESSION_STATE
E0804 21:23:34.542371 22200 quorum_cluster.cc:349] Zookeeper session timeout.
I0804 21:23:34.542377 22200 quorum_cluster.cc:443] QuorumCluster state change: Running_Connecting --> Expired
I0804 21:23:34.542388 22200 quorum_server.cc:64] Get quorum cluster event: EXPIRED
W0804 21:23:34.542394 22200 quorum_server.cc:79] Session expired.
I0804 21:23:34.542402 22200 quorum_server.cc:42] Stop quorum server:
Traceback:
[EAgain]:
I0804 21:23:34.542412 22200 quorum_cluster.cc:208] Stopping quorum cluster.
I0804 21:23:34.542415 22200 quorum_cluster.cc:443] QuorumCluster state change: Expired --> Stopped
I0804 21:23:34.542420 22200 quorum_cluster.cc:210] Sending stopping signal to upper layer.
I0804 21:23:34.542428 22200 quorum_server.cc:64] Get quorum cluster event: STOPPED
```

```bash
# node3
# 成为 leader
I0804 21:21:58.612960 31567 meta_server.cc:289] Meta rpc server starts at 172.16.91.3:10100
I0804 21:23:34.126381 19959 quorum_cluster.cc:698] Ger priority config:
I0804 21:23:34.126585 19959 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 53
I0804 21:23:34.126611 19959 quorum_server.cc:64] Get quorum cluster event: ROLE_CHANGED
W0804 21:23:34.126619 19959 quorum_server.cc:72] Role changed.
W0804 21:23:34.126624 19959 quorum_server.cc:94] Reset service.
```

# detector

```bash
# ./detector watch
2022/08/05 00:20:11.245854 is leader changed, current: true
W0805 00:23:23.902854    7158 client.go:290] [zbs-client] Invoke reaches the deadline and returns, method=/zbs.meta.StatusService/GetMetaSummary
2022/08/05 00:23:24.904701 is leader changed, current: false

I0805 00:22:45.506664  3257 quorum_cluster.cc:443] QuorumCluster state change: Running_Connecting --> Running
W0805 00:23:16.301744  3257 zookeeper.cc:220] zookeeper session event: CONNECTED_STATE --> CONNECTING_STATE
W0805 00:23:16.301755  3257 zookeeper.cc:183] Start the session timeout timer: 3600
I0805 00:23:16.301772  3257 quorum_cluster.cc:443] QuorumCluster state change: Running --> Running_Connecting
W0805 00:23:19.901795  3257 zookeeper.cc:199] Stop the session timeout timer.
I0805 00:23:19.902019  3257 quorum_cluster.cc:210] Sending stopping signal to upper layer. # 已经停止
W0805 00:23:24.486732  3257 zookeeper.cc:199] Stop the session timeout timer.
```

```bash
# ./detector watch
2022/08/05 00:23:21.125860 is leader changed, current: true

I0805 00:23:20.124768 19959 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 58
```

```bash
ifdown ens224; sleep 4; ifup ens224
```

```
# node2
$./detector watch
2022/08/05 00:51:57.688991 is leader changed, current: true
W0805 00:52:21.858488    8436 client.go:290] [zbs-client] Invoke reaches the deadline and returns, method=/zbs.meta.StatusService/GetMetaSummary
2022/08/05 00:52:22.861483 is leader changed, current: false

I0805 00:51:50.256310  8423 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 60
I0805 00:51:50.545315  8434 meta_server.cc:289] Meta rpc server starts at 172.16.91.2:10100

I0805 00:52:17.857874  8423 quorum_cluster.cc:208] Stopping quorum cluster.
```

```
# node3
[root@fanyang-meta3 00:51:54 ~]$./detector watch
2022/08/05 00:52:19.125653 is leader changed, current: true

I0805 00:52:18.124378  6655 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 61


```

```bash
# node2

```

```bash
# node3
I0805 00:57:13.768395  6899 quorum_cluster.cc:518] [ROLE CHANGE] I am the leader now.  seq is 63


```
