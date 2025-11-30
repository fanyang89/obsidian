#ZooKeeper

环境信息对照表

| myid | Sg IP         |         Mgt IP |
| :--- | ------------- | -------------: |
| 1    | 10.231.19.164 | 192.168.26.181 |
| 2    | 10.231.19.165 | 192.168.29.217 |
| 3    | 10.231.19.166 |  192.168.24.54 |
| 4    | 10.231.19.167 | 192.168.28.104 |
| 5    | 10.231.19.168 | 192.168.26.173 |

日志：/Users/fanyang/logstorage/zookeeper-lost-member

我用嵌套集群升级 5.0.5 版本，升级到 TASK [update third-party rpm]后失败了，再次升级 pre_check zookeeper 配置文件不正确，配置文件中少了一个节点 10.231.19.167 麻烦帮忙看下这个问题

 
这个从升级脚本层面未找到什么线索，集群升级失败就是上面在进行 zk 配置更新及软件包升级服务重启阶段失败导致的。目前从 `zbs-tool zk list` 查看 zk 集群没发现什么异常。  
查看 192.168.29.217（滚动升级各节点 zk 时失败的 10.231.19.165 节点） 的 zk log 存在部分 WARN 级别日志。  
这个请 [@fanyang](https://smartx1.slack.com/team/UEBE4B3U7) 来帮忙看下吧。

我不确定在升级过程中是否有过短暂网络中断，我升级过程中连了 ssh，后来我再去看升级情况的时候发现 ssh 自己失去连接了，会不会是节点有过网络中断导致的，可以在哪里确认一下升级过程中是否有网络中断呢

失败是在滚动升级 zk 第一个节点 10.231.19.165 时，重启 zk 后循环检查 zk 状态不符预期失败中断的；  
`zbs-tool zk list` 输出 zk 集群状态正常；
但对应 zk 配置中的 server 缺少了 10.231.19.167 节点；  
具体动态配置为何缺少 167 节点目前没找到什么线索，当前对应的 dynamic 配置文件修改时间是 18:19 ，非当时失败时刻（16:50）。

![](assets/Pasted%20image%2020220815163410.png)

![](assets/Pasted%20image%2020220815163420.png)

```
// node4 此时得知自己已经不在集群
2022-08-12T18:19:43,978 [myid:] - INFO [main:QuorumPeerConfig@136] - Reading configuration from: /etc/zookeeper/zoo.cfg
2022-08-12T18:19:45,059 [myid:] - INFO [WorkerReceiver[myid=4]:FastLeaderElection$Messenger$WorkerReceiver@300] - 4 Received version: 30000042e my version: 100000000
```

```
$ rg '/zookeeper/config' -A 4 | grep mtime | awk -F= '{print $2}'
 Fri Aug 12 18:19:52 CST 2022
 Fri Aug 12 18:19:50 CST 2022
 Mon Aug 15 14:55:05 CST 2022
 Fri Aug 12 18:19:52 CST 2022
 Fri Aug 12 18:19:50 CST 2022
 Thu Aug 04 20:30:28 CST 2022
 Fri Aug 12 18:19:50 CST 2022
 Fri Aug 12 18:19:49 CST 2022
 Mon Aug 15 20:02:45 CST 2022
 Fri Aug 12 18:19:50 CST 2022
 Fri Aug 12 18:19:52 CST 2022
 Fri Aug 12 18:19:50 CST 2022
 Fri Aug 12 18:20:00 CST 2022
 Fri Aug 12 18:20:00 CST 2022
 Mon Aug 15 14:55:05 CST 2022
```

说明，在 Fri Aug 12 18 :20: 00 CST 2022 左右，发生了一件事情，让所有 server 都 apply 了新的 config

```
2022-08-12T18:19:43,658 [myid:] - INFO [WorkerReceiver[myid=2]:FastLeaderElection@706] - Notification: 2 (message format version), 2 (n.leader), 0x20000270f (n.zxid), 0x1 (n.round), LOOKING (n.state), 2 (n.sid), 0x2 (n.peerEPoch), LOOKING (my state)30000042e (n.config version)
```

```
22-8-12 下午06时23分55秒 session 0x20000020b46001c cxid 0x0 zxid 0x30000042d createSession 5000
22-8-12 下午06时23分55秒 session 0x20000020b46001c cxid 0x38 zxid 0x30000042e closeSession null
```

```
2022-08-12T18:23:54,860 [myid:] - INFO [CommitProcWorkThread-1:LearnerSessionTracker@121] - Committing global session 0x20000020b46001c
```

```
2022-08-12T18:23:55,801 [myid:] - INFO [NIOWorkerThread-1:QuorumZooKeeperServer@157] - Submitting global closeSession request for session 0x20000020b46001c
```

```
2022-08-12T18:04:57,698 [myid:] - INFO  [WorkerReceiver[myid=1]:FastLeaderElection@706] - Notification: 2 (message format version), 2 (n.leader), 0x20000270f (n.zxid), 0x2 (n.round), LOOKING (n.state), 2 (n.sid), 0x2 (n.peerEPoch), LEADING (my state)30000042e (n.config version)
2022-08-12T18:04:57,700 [myid:] - ERROR [LearnerHandler-/10.231.19.165:34608:LearnerHandler@629] - Unexpected exception causing shutdown while sock still open
java.io.IOException: Follower is ahead of the leader (has a later activated configuration)
	at org.apache.zookeeper.server.quorum.LearnerHandler.run(LearnerHandler.java:398) [zookeeper-3.5.9.jar:3.5.9-4c6bfe85d5c72344cb2ffc40f50a9dacf152d7fd]
```

```
[root@add-test-7-master 16:59:51 smartx]$cat /etc/zbs/zbs.conf
[network]
data_ip = 10.231.19.201
heartbeat_ip = 10.231.19.201
vm_ip = 192.168.30.40
web_ip = 192.168.30.40

[cluster]
role = master
members = 10.231.19.165,10.231.19.164,10.231.19.167,10.231.19.166,10.231.19.201
zookeeper = 10.231.19.165:2181,10.231.19.164:2181,10.231.19.167:2181,10.231.19.166:2181,10.231.19.201:2181
mongo = 10.231.19.201:27017,10.231.19.166:27017,10.231.19.167:27017,10.231.19.165:27017,10.231.19.164:27017
cluster_mgt_ips = 192.168.24.54,192.168.26.181,192.168.28.104,192.168.29.217,192.168.30.40,192.168.31.244
cluster_storage_ips = 10.231.19.166,10.231.19.164,10.231.19.167,10.231.19.165,10.231.19.201,10.231.19.200

[root@add-test-7-master 17:00:03 smartx]$zbs-tool zk list
10.231.19.165:2181	  follower	0x40001dac3
10.231.19.164:2181	  follower	0x40001dac3
10.231.19.167:2181	  follower	0x40001dac3
10.231.19.166:2181	  follower	0x40001dac3
10.231.19.201:2181	  follower	0x40001dac3
[root@add-test-7-master 17:00:38 smartx]$
```
