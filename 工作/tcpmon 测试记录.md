17.95 集群主节点：

- 17.96
- 17.97

http://192.168.90.221/f/testing/tcpmon/tcpmon-0.9.4-4.el7.x86_64.rpm

```
$ zbs-tool zk list
192.168.17.96  10.255.0.96:2181          follower      0x11100200bfb
192.168.17.97  10.255.0.97:2181          follower      0x11100200bfb
192.168.17.109 10.255.0.109:2181         follower      0x11100200bfb
192.168.19.41	 10.255.0.141:2181         follower      0x11100200bfb
192.168.19.43  10.255.0.143:2181           leader      0x11100200bfb
```

```bash
curl -JLO http://192.168.17.96:6789/backup
curl -JLO http://192.168.17.97:6789/backup
curl -JLO http://192.168.17.109:6789/backup
curl -JLO http://192.168.19.41:6789/backup
curl -JLO http://192.168.19.43:6789/backup
```

```
[root@dogfood-idc-elf-109-GPU-A16 18:33:06 ~]$grep 'Exception when following the leader' /var/log/zookeeper/zookeeper.log
2023-10-12T09:48:39,123 [myid:] - WARN  [QuorumPeer[myid=7](plain=10.255.0.109:2181)(secure=disabled):Follower@100] - Exception when following the leader
```

```
[smartx@dogfood-idc-elf-19-41 09:41:04 ~]$sudo grep 'Exception when following the leader' /var/log/zookeeper/zookeeper.log
2023-10-13T01:00:29,502 [myid:] - WARN  [QuorumPeer[myid=8](plain=10.255.0.141:2181)(secure=disabled):Follower@100] - Exception when following the leader
```

```
2023-10-17T14:50:48+08:00 INF cmd/export2.go:69 > File ignored since there is no target time point in it file=tcpmon-dataf-197
2023-10-17T14:55:22+08:00 INF cmd/export2.go:78 > File exported with target time point file=tcpmon-dataf-198
Elapsed: 4m34s
```

```
2023-10-17T14:59:52+08:00 INF cmd/export2.go:69 > File ignored since there is no target time point in it file=tcpmon-dataf-197
2023-10-17T15:00:03+08:00 INF cmd/export2.go:78 > File exported with target time point file=tcpmon-dataf-198
Elapsed: 11s
```

# 变更

去掉了 `ss -4`
更新时间点：`Thu Oct 19 12:50:52 CST 2023`
等待下个故障出现。

# 新的风暴！

```
[root@dogfood-idc-elf-19-41 14:57:47 ~]$
2023-10-20T13:38:53,486 [myid:] - ERROR [LearnerHandler-/10.255.0.143:41668:LearnerHandler@629] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
--
2023-10-20T13:39:28,618 [myid:] - ERROR [LearnerHandler-/10.255.0.143:32828:LearnerHandler@629] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
```

```
zookeeper-3.5.9-18.smartx.x86_64
jdk1.8.0_131-1.8.0_131-fcs.x86_64
```

# 更新到 v19

```
[root@dogfood-idc-elf-96 18:17:49 zookeeper]$date
Mon Oct 23 18:18:03 CST 2023
```

```bash
# 9 10 7 13 14
sudo grep 'Read timed out' /var/log/zookeeper/zookeeper.log
```

# Day 3

```
[smartx@dogfood-idc-elf-19-43 10:40:14 ~]$sudo grep -A1 -B1 'Read timed out' /var/log/zookeeper/zookeeper.log
2023-10-24T07:01:13,084 [myid:] - ERROR [LearnerHandler-/10.255.0.141:33556:LearnerHandler@615] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
--
2023-10-24T10:32:31,666 [myid:] - ERROR [LearnerHandler-/10.255.0.141:41168:LearnerHandler@615] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
--
2023-10-24T10:32:42,971 [myid:] - ERROR [LearnerHandler-/10.255.0.141:56950:LearnerHandler@615] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
--
2023-10-24T10:36:22,892 [myid:] - ERROR [LearnerHandler-/10.255.0.141:57366:LearnerHandler@615] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
--
2023-10-24T10:39:17,733 [myid:] - ERROR [LearnerHandler-/10.255.0.141:41480:LearnerHandler@615] - Unexpected exception causing shutdown while sock still open
java.net.SocketTimeoutException: Read timed out
        at java.net.SocketInputStream.socketRead0(Native Method) ~[?:1.8.0_131]
```

```
cd /var/lib/tcpmon
tar -cf tcpmon-db-$(hostname).tar db/
tar -czf zookeeper-log-$(hostname).tar.gz /var/log/zookeeper /etc/zookeeper /var/lib/zbs/metad/zookeeper/version-2
```

```bash
systemctl restart irqbalance
cat /etc/cgconfig.d/cpuset.conf | grep -A2 'meta\|chunk'

cat /etc/sysconfig/irqbalance
19.41 19.43
IRQBALANCE_BANNED_CPUS=100003

17.96 17.97 17.109
CPU(s):                80
0 1 2
IRQBALANCE_BANNED_CPUS=7

# 操作时间
[root@dogfood-idc-elf-19-43 16:31:36 ~]$date
Tue Oct 24 16:31:44 CST 2023
```

![](assets/Pasted%20image%2020231024181741.png)

![](assets/Pasted%20image%2020231024182009.png)

![](assets/Pasted%20image%2020231024182136.png)

![](assets/Pasted%20image%2020231024183755.png)
根据 `RetransTotal` 可以看到，重传发生的间隔：

- `10:31:49~10:31:50` 重传 1 个
- `10:31:52~10:31:53` 重传 1 个 ~4s
- `10:31:54~10:31:55` 重传 1 个 ~3s
- `10:32:01~10:32:02` 重传 1 个 ~8s
- `10:32:20~10:32:21` 重传 1 个 ~20s

```
2023-10-24T17:51:04.410+0800: 85165.817: [GC (Allocation Failure) 2023-10-24T17:51:04.410+0800: 85165.817: [DefNew: 147487K->7751K(157248K)
, 0.0099672 secs]2023-10-24T17:51:04.420+0800: 85165.827: [Tenured: 349655K->31655K(349760K), 0.1977744 secs] 496682K->31655K(507008K), [Me
taspace: 15997K->15997K(1064960K)], 0.2079389 secs] [Times: user=0.14 sys=0.06, real=0.21 secs]
```

```
root@fanyang-arc:~/data # rg 'real=1.19'
2023-10-25T09:34:55.761+0000: 3181.004: [Full GC (Ergonomics) [PSYoungGen: 232448K->147728K(412160K)] [ParOldGen: 1228786K->1228665K(1228800K)] 1461234K->1376394K(1640960K), [Metaspace: 16501K->16501K(1064960K)], 1.1882767 secs] [Times: user=3.84 sys=0.30, real=1.19 secs]

root@fanyang-arc:~/data # rg 'real=1.23'
2023-10-25T08:41:51.730+0000: 3.113: [GC (Allocation Failure) [PSYoungGen: 283839K->21478K(284672K)] 391035K->213657K(634368K), 1.2315290 secs] [Times: user=5.79 sys=9.66, real=1.23 secs]

root@fanyang-arc:~/data # rg 'real=1.41'
2023-10-25T09:44:48.819+0000: 3780.203: [Full GC (Ergonomics) [PSYoungGen: 204800K->148570K(409600K)] [ParOldGen: 1228615K->1228733K(1228800K)] 1433415K->1377303K(1638400K), [Metaspace: 16701K->16701K(1064960K)], 1.4087995 secs] [Times: user=3.00 sys=0.00, real=1.41 secs]
```

# 开始抓包

```bash
-W file count
-G rorate_seconds
-C file size (in MB)

$ tcpdump -i port-storage -s 96 'tcp port 2888 or tcp port 3888' -z gzip -W 10 -G 3600 -C 500 -w 'tcpdump-trace-%y%m%d-%H%M%S.pcap' -Z root
dropped privs to root
tcpdump: listening on port-storage, link-type EN10MB (Ethernet), snapshot length 96 bytes

# date
Thu Oct 26 16:22:11 CST 2023
```

```bash
# grep GOODBYE /var/log/zookeeper/zookeeper.log | tail -n 2
2023-10-26T17:51:46,625 [myid:] - WARN  [LearnerHandler-/10.255.0.141:37630:LearnerHandler@630] - ******* GOODBYE /10.255.0.141:37630 ********
2023-10-26T17:55:44,304 [myid:] - WARN  [LearnerHandler-/10.255.0.141:42652:LearnerHandler@630] - ******* GOODBYE /10.255.0.141:42652 ********

# grep 'Exception when following the leader' /var/log/zookeeper/zookeeper.log | tail -n 2
2023-10-26T17:51:46,648 [myid:] - WARN  [QuorumPeer[myid=8](plain=10.255.0.141:2181)(secure=disabled):Follower@100] - Exception when following the leader
2023-10-26T17:55:44,888 [myid:] - WARN  [QuorumPeer[myid=8](plain=10.255.0.141:2181)(secure=disabled):Follower@100] - Exception when following the leader
```

```
2023-10-26T17:55:44,304 [myid:] - WARN  [LearnerHandler-/10.255.0.141:42652:LearnerHandler@630] - ******* GOODBYE /10.255.0.141:42652 ********
```

```bash
# 19-43.pcap

903331	1537.069	09:55:44.304	10.255.0.143	10.255.0.141	TCP	66	2888 → 42652 [FIN, ACK] Seq=726305 Ack=52125 Win=50688 Len=0 TSval=508197445 TSecr=2152584878
```

19.43 pcap
![](assets/Pasted%20image%2020231026222153.png)

```java
 public void serialize(OutputArchive a_, String tag) throws java.io.IOException {
     a_.startRecord(this, tag);
     a_.writeInt(type, "type");    // 4
     a_.writeLong(zxid, "zxid");   // 8
     a_.writeBuffer(data, "data"); // 4 + buf.len
     {
         a_.startVector(authinfo, "authinfo"); // 4 + buf.len
         if (authinfo != null) {
             int len1 = authinfo.size();
             for (int vidx1 = 0; vidx1 < len1; vidx1++) {
                 org.apache.zookeeper.data.Id e1 = (org.apache.zookeeper.data.Id) authinfo.get(vidx1);
                 a_.writeRecord(e1, "e1");
             }
         }
         a_.endVector(authinfo, "authinfo");
     }
     a_.endRecord(this, tag);
 }

 public void deserialize(InputArchive a_, String tag) throws java.io.IOException {
     a_.startRecord(tag);
     type = a_.readInt("type");         // 4
     zxid = a_.readLong("zxid");        // 8
     data = a_.readBuffer("data");      // 4 + buf.len
     {
         Index vidx1 = a_.startVector("authinfo"); // 4 + buf.len
         if (vidx1 != null) {
             authinfo = new java.util.ArrayList<org.apache.zookeeper.data.Id>();
             for (; !vidx1.done(); vidx1.incr()) {
                 org.apache.zookeeper.data.Id e1;
                 e1 = new org.apache.zookeeper.data.Id();
                 a_.readRecord(e1, "e1");
                 authinfo.add(e1);
             }
         }
         a_.endVector("authinfo");
     }
     a_.endRecord(tag);
 }
```

![|500](assets/Pasted%20image%2020231027120335.png)

```bash
grep 'real' /var/log/zookeeper/gc.log.0.current  | awk '{print $(NF-1)}' | sort -n | uniq -c
```

要点：
Long session id 出现 hash 碰撞
如何碰撞？高 32 位和低 32 位交换，或者 0 和 1 互换
session id 组成

- 高8位代表创建 Session 时所在的 zk 节点的 id
- 中间40位代表zk节点当前角色在创建的时候的时间戳
- 低 16 位是一个计数器，初始值为0

例如

```
s=0x2001776d0d90000
```

```java
2023-10-27T15:40:54,695 [myid:] - WARN  [QuorumPeer[myid=8](plain=10.255.0.141:2181)(secure=disabled):Learner@706] - Learner ping writePacket() takes 0ms, data
len: 48, view: 0ms, data: AwCO4WgXCLEAAAfQCAAAov6oJMQAAAfQBwKV6mzCdeUAAAu4CQK8fRWReIEAAAu4
2023-10-27T15:40:55,163 [myid:] - WARN  [QuorumPeer[myid=8](plain=10.255.0.141:2181)(secure=disabled):Follower@123] - Follower ping() takes 468
```

```
[root@dogfood-idc-elf-19-41 16:53:03 fanyang]$systemctl restart zookeeper
[root@dogfood-idc-elf-19-41 16:53:09 fanyang]$date
Fri Oct 27 16:53:18 CST 2023
```

```
2023-10-27T18:47:16,834 [myid:] - INFO  [main:QuorumPeerConfig@137] - Reading configuration from: /etc/zookeeper/zoo.cfg
```

![|500](assets/Pasted%20image%2020231030101640.png)

![|600](assets/Snipaste_2023-10-27_12-38-38.png)

```
2023-10-29T21:08:07.590+0800: 181251.642: [GC (Allocation Failure) 2023-10-29T21:08:07.590+0800: 181251.642: [DefNew: 144922K->4957K(157376K), 0.0059715 secs] 243454K->103767K(506944K), 0.0060672 secs] [Times: user=0.01 sys=0.00, real=0.00 secs]
2023-10-29T21:09:09.714+0800: 181313.767: [GC (Allocation Failure) 2023-10-29T21:09:09.714+0800: 181313.767: [DefNew: 144861K->5137K(157376K), 0.0063592 secs] 243671K->104230K(506944K), 0.0064391 secs] [Times: user=0.00 sys=0.00, real=0.01 secs]
2023-10-29T21:20:03.650+0800: 181967.702: [GC (Allocation Failure) 2023-10-29T21:20:03.650+0800: 181967.702: [DefNew: 145041K->7433K(157376K), 0.0119320 secs] 244134K->106847K(506944K), 0.0126431 secs] [Times: user=0.01 sys=0.00, real=0.01 secs]
```

```
[root@dogfood-idc-elf-19-41 16:45:12 fanyang]$rpm -Uvh ./zookeeper-*rpm --force
Preparing...                          ################################# [100%]
Updating / installing...
   1:zookeeper-lib-3.5.9-19.smartx    ################################# [ 50%]
   2:zookeeper-3.5.9-19.smartx        ################################# [100%]
[root@dogfood-idc-elf-19-41 16:45:28 fanyang]$systemctl restart zookeeper
[root@dogfood-idc-elf-19-41 16:45:35 fanyang]$date
Mon Oct 30 16:45:42 CST 2023

[root@dogfood-idc-elf-19-41 16:46:23 fanyang]$systemctl status zookeeper
● zookeeper.service - Zookeeper distributed coordination server
   Loaded: loaded (/usr/lib/systemd/system/zookeeper.service; enabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/zookeeper.service.d
           └─cgroup.conf
   Active: active (running) since Mon 2023-10-30 16:45:35 CST; 50s ago
  Process: 58085 ExecStop=/usr/lib/zookeeper/bin/zkServer.sh stop (code=exited, status=0/SUCCESS)
  Process: 58266 ExecStart=/usr/lib/zookeeper/bin/zkServer.sh --config /etc/zookeeper start (code=exited, status=0/SUCCESS)
 Main PID: 58329 (java)
   Memory: 142.4M
   CGroup: /system.slice/system-zbs.slice/system-zbs-metamain.slice/zookeeper.service
           └─58329 java -Dzookeeper.log.dir=/var/log/zookeeper -Dzookeeper.log.file=zookeeper.log -Dzookeep...

Oct 30 16:45:34 dogfood-idc-elf-19-41 systemd[1]: Unit zookeeper.service entered failed state.
Oct 30 16:45:34 dogfood-idc-elf-19-41 systemd[1]: zookeeper.service failed.
Oct 30 16:45:34 dogfood-idc-elf-19-41 systemd[1]: Starting Zookeeper distributed coordination server...
Oct 30 16:45:34 dogfood-idc-elf-19-41 zookeeper[58266]: /usr/bin/java
Oct 30 16:45:34 dogfood-idc-elf-19-41 zookeeper[58266]: JMX disabled by user request
Oct 30 16:45:34 dogfood-idc-elf-19-41 zookeeper[58266]: Using config: /etc/zookeeper/zoo.cfg
Oct 30 16:45:35 dogfood-idc-elf-19-41 systemd[1]: Started Zookeeper distributed coordination server.
```

```bash
[root@dogfood-idc-elf-19-41 11:25:20 fanyang]$systemctl restart zookeeper
[root@dogfood-idc-elf-19-41 11:25:27 fanyang]$date
Tue Oct 31 11:25:30 CST 2023
# grep 'Exception when following the leader' /var/log/zookeeper/zookeeper.log
```
