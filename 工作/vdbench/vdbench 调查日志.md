#vdbench

# 引用

[2022-07-13 数据不一致](工作/vdbench/2022-07-13%20数据不一致.md)

# 思考

写入大概经过的模块：

- vdbench write
- qemu+libiscsi
- 接入 access
- lsm

问题 1：vdbench 写了什么？通过 vdbench journal 解决。
问题 2：zbs 收到了什么 IO？增加 write journal 解决。每次收到写入，都记录在文件中。

# 当前

可复现的版本：4.0.13-rc3
不可复现版本：master

master -- 4.0.13-rc3

# New Day 1

vm1:

- /dev/vdb virtio
- /dev/sda SCSI
- /dev/sdb IDE
  vm2:
- /dev/vdb virtio
- /dev/sda IDE
- /dev/sdb SCSI
  vm3:
- /dev/vdb virtio
- /dev/sda SCSI
- /dev/sdb IDE

![](assets/Pasted%20image%2020220922162421.png)

```bash
for i in {1..300}; do
	date
	printf '第 %d 次数据网重启\n' $i
	systemctl restart network
	sleep 300
done

for i in {1..100};do
	date
	printf '第 %d 次故障zbs-metad&zbs-chunkd\n' $i
	systemctl stop zbs-metad
	systemctl stop zbs-chunkd
	sleep 60
	date
	printf '第 %d 次恢复zbs-metad&zbs-chunkd\n' $i
	systemctl start zbs-metad
	systemctl start zbs-chunkd
	sleep 300
done
```

# Final Day (maybe?)

错误时间点：2022-09-26 23:40:07

```
# ./vdbench printjournal  ./fanyang_journal/sd1.jnl  | grep '0x7f'
16:57:09.429 Starting lba: 0x0000057e0000 key: 0x7f
16:57:09.429 Starting lba: 0x0000057e8000 key: 0x7f
16:57:09.429 Starting lba: 0x0000057f0000 key: 0x7f
16:57:09.429 Starting lba: 0x0000057f8000 key: 0x7f
```

`0x7f` 表示这是一个 bad sector

```java
public static final int DV_ERROR      = 0x7f; // 127;
```

观察日志

```bash
# 测试脚本
Mon Sep 26 23:39:37 CST 2022
第 3 次数据网重启
Mon Sep 26 23:44:58 CST 2022
第 4 次数据网重启

# chunk
I0926 23:39:39.669832 73305 data_channel_server_v2.cc:105] [DATACHANNEL SERVER] lost server_transport 0x55b8d8e07440 ServerTransport with peer TCP Socket (192.168.67.96:35034) via local TCP Socket (0.0.0.0:10201)
# 38+12=:50 session expired

# journalctl -u network
Sep 26 23:39:37 Node94 systemd[1]: Stopping LSB: Bring up/down networking...
# ...
Sep 26 23:39:58 Node94 systemd[1]: Started LSB: Bring up/down networking.
# 耗时 21s

# chunk
W0926 23:39:51.788976 73305 data_channel_manager.cc:207] Fail to ping : 172.91.67.95:10201 ping error times: 3 st:
W0926 23:39:51.789006 73305 data_channel_manager.cc:207] Fail to ping : 172.91.67.96:10201 ping error times: 4 st:
W0926 23:39:51.789029 73305 data_channel_manager.cc:207] Fail to ping : 192.168.67.96:10201 ping error times: 4 st:
I0926 23:39:52.162174 73305 data_channel_server_v2.cc:98] [DATACHANNEL SERVER] accepted new server_transport 0x55b8d9722b40 ServerTransport with peer TCP Socket (192.168.67.96:44144) via local TCP Socket (0.0.0.0:10201)
I0926 23:39:55.293725 73305 data_channel_manager.cc:219] datachannel back to normal : ip : 192.168.67.96 port 10201
I0926 23:39:58.791986 73305 data_channel_manager.cc:219] datachannel back to normal : ip : 172.91.67.95 port 10201
I0926 23:39:58.795619 73305 data_channel_manager.cc:219] datachannel back to normal : ip : 172.91.67.96 port 10201
# 可以看到，从断开到恢复连接，耗时 58-39=19s

Mon Sep 26 23:40:53 CST 2022 第 3 次故障zbs-metad&zbs-chunkd
Mon Sep 26 23:41:54 CST 2022 第 3 次恢复zbs-metad&zbs-chunkd

# 55-37=18
# NOP_INTERVAL=5000ms
2022-09-26T15:39:55.360855Z qemu-kvm: iSCSI: NOP timeout. Reconnecting...

# chunk
W0926 23:39:58.871896 73316 zookeeper.cc:220] zookeeper session event: CONNECTING_STATE --> CONNECTED_STATE
W0926 23:39:58.981452 73316 session_follower.cc:619] [SESSION EXPIRED]:  response: , st:
I0926 23:39:59.946116 73316 session_follower.cc:236] [FOLLOWER SESSION RECONNECT]
# kLeaseIntervalMS = 12000
I0926 23:40:00.053925 73316 session_follower.cc:170] Try to reset session client to ip: 172.91.67.96 port: 10103
I0926 23:40:06.091338 73316 session_follower.cc:177] Session Client was resetted
# session client 恢复时间 29s

# vdbench 在 23:40:07 报错，距离 23:39:37 重启 network 开始，间隔 30s
# 23:39:37~23:40:07  15:39:37~15:40:07
```

问题：vdbench 进行 I/O 超时时间为？
I/O 路径：
vdbench -> SCSI -> libiscsi -> ZBS
iSCSI Nop-in timeout = 30s
iSCSI Nop-out timeout = 90s

NOP-In: A No Operation request sent from the target to the initiator.
NOP-Out: A No Operation request sent from the initiator to the target.

initiator = QEMU 虚拟机，libiscsi
target = ZBS

NOP-in 是 ZBS 给 libiscsi 发送。

```cpp
DEFINE_int32(iscsi_nopin_interval,
             gflags::Int32FromEnv("ISCSI_NOPIN_INTERVAL", 30),
             "iscsi nopin interval");

DEFINE_int32(iscsi_timeout, gflags::Int32FromEnv("ISCSI_TIMEOUT", 60),
             "iscsi timeout");

DEFINE_int32(iscsi_nopin_timeout, gflags::Int32FromEnv("ISCSI_NOPIN_TIMEOUT", 5),
             "iscsi nopin timeout");
```

Access Control 用于根据 Session 状态控制 IO 使能状态。Session Handler 在处理 Session 状态变化时会同步更新 Access Control 的状态。每次 IO 执行之前都需要通过  Access Control 检查，如果 Session 处于 Jeopardy 或 Init 状态，所有 IO 将放入队列中等待下个状态（下个状态是正常则继续执行，Expire 则直接返回失败）。如果 Session 处于 Expired 状态则直接返回失败。

- ZBS-20220 nfs: set IO timeout for IOController
- ZBS-14279 zbs_client: remove the retry times

特征：

```bash
22:47:13.006           8        0.0     0.00       0   0.00    0.000    0.000    0.000     0.00     0.00    0.000   32.0   4.0   0.3
22:47:31.003          26        0.0     0.00       0   0.00    0.000    0.000    0.000     0.00     0.00    0.000   32.0   3.0   0.3
# 18s


22:52:34.002         329        0.0     0.00       0   0.00    0.000    0.000    0.000     0.00     0.00    0.000   32.0   1.0   0.3
22:52:50.002
# 16s
```
