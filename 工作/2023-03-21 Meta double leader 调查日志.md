---
title: 背景
---

[Slack](https://smartx1.slack.com/archives/C01D4KNCZQE/p1678882542309149)

疑似出现 meta double leader，开始调查。

# 背景

注入故障：242 和 245 当时均非 metaleader/zkleader。

```bash
2023-03-15 01:18:36,588|3658843:ThreadPoolExecutor-747_0:140040269186624|root|INFO|[baseLinux.py:53]|Running: iptables -t mangle -A OUTPUT -s 10.0.131.242 -j DROP with conn: <Connection host=172.20.131.242 user=smartx>;
2023-03-15 01:18:36,588|3658843:ThreadPoolExecutor-747_1:140039715530304|root|INFO|[baseLinux.py:53]|Running: iptables -t mangle -A OUTPUT -s 10.0.131.245 -j DROP with conn: <Connection host=172.20.131.245 user=smartx>;

# 恢复
2023-03-15 01:24:05,131|3658843:MainThread:140042024847168|root|INFO|[conftest.py:60]|teardown: RESTORE_IPTABLES
```

故障注入：247 和 243 当时分别是 zk leader 和 meta leader。

```bash
2023-03-15 01:24:27,048|3658843:ThreadPoolExecutor-780_0:140039707137600|root|INFO|[baseLinux.py:53]|Running: iptables -t mangle -A OUTPUT -s 10.0.131.247 -j DROP with conn: <Connection host=172.20.131.247 user=smartx>;
2023-03-15 01:24:27,048|3658843:ThreadPoolExecutor-780_1:140039749101120|root|INFO|[baseLinux.py:53]|Running: iptables -t mangle -A OUTPUT -s 10.0.131.243 -j DROP with conn: <Connection host=172.20.131.243 user=smartx>;

2023-03-15 01:29:53,025|3658843:MainThread:140042024847168|root|INFO|[conftest.py:60]|teardown: RESTORE_IPTABLES
```

注入：244 和 246 均为 storage 角色

```bash
2023-03-15 01:29:57,943|3658843:ThreadPoolExecutor-813_0:140039220622912|root|INFO|[baseLinux.py:53]|Running: /home/smartx/python3_venv/venv/bin/tcset eth1 --change --loss 10% --src-network 10.0.131.246 --network 10.0.131.244 with conn: <Connection host=172.20.131.246 user=smartx>;
```

查看得到，247 节点中 extent 变为 invalid 时，meta leader 是 242 节点。(但在 meta 日志中在该时间没找到 `pid: 65380` 或 volume id{`45f285b4-c786-4109-9007-910b4c29fc43`} 的相关日志。)

```bash
# /var/log/zbs/zbs-metad.log.20230314-235823.160199
I0315 01:24:35.029403 235064 meta_server.cc:408] Start the leader role
```

# 问题列表

- 是否出现 meta double leader？

# 检查 pid 65380 生命周期

```cpp
E0314 03:02:20.291482 46399 pextent_allocator.cc:256] There are no enough space or hosts for the temporary replica allocation: total: 214742073344 allocated: 7633895424 provisioned: 38654705664 current alive chunks num: 2 st: [UAllocFail]
Traceback: [EAllocSpace]:
I0314 03:02:20.291517 46399 meta_rpc_server.cc:1921] pid: 65380 epoch: 70271 failed to alloc temporary_replica for failed cid: 2 [UAllocFail]
Traceback: [EAllocSpace]:
```

```bash
-> % rg 'pid: 65380' -l | grep -v bash | grep -v ins | sort
172_20_131_241/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-102944.159401
172_20_131_241/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-111543.203029
172_20_131_241/zbs_logs/zbs-metad/zbs-metad.log.20230314-031904.42461
172_20_131_241/zbs_logs/zbs-metad/zbs-metad.log.20230314-111653.205910
172_20_131_241/zbs_logs/zbs-metad/zbs-metad.log.20230314-235820.229970

172_20_131_242/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230313-191714.170738
172_20_131_242/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-102558.170738
172_20_131_242/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-111542.235838
172_20_131_242/zbs_logs/zbs-metad/zbs-metad.log.20230314-044133.193452
172_20_131_242/zbs_logs/zbs-metad/zbs-metad.log.20230314-051229.217876

172_20_131_243/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-013912.110102
172_20_131_243/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-102039.227101
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230314-045518.31424
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230314-111700.38748
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230314-153257.38748
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230314-200004.38748
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230314-234541.38748
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230314-235826.220436
172_20_131_243/zbs_logs/zbs-metad/zbs-metad.log.20230315-040135.224360

172_20_131_244/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230311-225809.10486

172_20_131_245/zbs_logs/zbs-metad/zbs-metad.log.20230314-032312.181016
172_20_131_245/zbs_logs/zbs-metad/zbs-metad.log.20230314-044649.6438

172_20_131_246/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230311-225815.10391

172_20_131_247/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-010206.27737
172_20_131_247/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-102404.147087
172_20_131_247/zbs_logs/zbs-chunkd/zbs-chunkd.log.20230314-111543.173425
172_20_131_247/zbs_logs/zbs-metad/zbs-metad.log.20230314-021500.6842
172_20_131_247/zbs_logs/zbs-metad/zbs-metad.log.20230314-111708.176874
172_20_131_247/zbs_logs/zbs-metad/zbs-metad.log.20230315-001744.125467
```

```bash
# 241
I0314 10:29:53.919777 159414 access_handler.cc:1094] [EXTENT GC CMD] pid: 65380 epoch: 70271
I0314 10:29:53.924180 159424 extent_inode.cc:271] [EXTENT SET STATUS] pid: 65380 from: EXTENT_STATUS_ALLOCATED to: EXTENT_STATUS_INVALID
I0314 10:29:54.804096 159424 lsm.cc:4865] [FREE EXTENT] status: EXTENT_STATUS_INVALID pid: 65380 epoch: 70271 generation: 4 bucket_id: 3940
I0314 10:29:56.852530 159424 extent_inode.cc:347] [DELETE EXTENT INODE] status: EXTENT_STATUS_INVALID pid: 65380 epoch: 70271 generation: 4

I0314 11:53:55.297724 203115 lsm.cc:4823] [ALLOC EXTENT] status: EXTENT_STATUS_ALLOCATED pid: 65380 epoch: 70271 generation: 0 bucket_id: 3940

```

```bash
[1 2 3 5 7]
-> % fd metad.log | awk -F/ '{print $1}' | sort | uniq
172_20_131_241
172_20_131_242
172_20_131_243
172_20_131_245
172_20_131_247
```

确认双 meta leader 出现时间？

检查时钟误差：

```bash
-> % grep 'offset:' `fd zbs-ntpd.log` | awk '{print $(NF-1)}' | sort -n | uniq | datamash max 1 min 1 mean 1 median 1
0.055   -1.434  -0.11741764705882       -0.0575
```

可认为误差较低。

```bash
# 241
-> % rg 'I am the leader now.  seq is' `fd metad.log` | grep 'seq is ' | awk '{print $NF}' | sort -n | tr '\n' ' '
508 524 652 737 741 792 799 853 864 871 1001 1026 1031 1277 1290 1297 1303 1308 1405
# 242
-> % rg 'I am the leader now.  seq is' `fd metad.log` | grep 'seq is ' | awk '{print $NF}' | sort -n | tr '\n' ' '
607 707 716 797 845 849 857 857 1022 1029 1101 1204 1278 1298 1304 1309
# 243
-> % rg 'I am the leader now.  seq is' `fd metad.log` | grep 'seq is ' | awk '{print $NF}' | sort -n | tr '\n' ' '
377 377 377 377 377 377 377 377 377 377 377 377 377 377 377 377 377 692 703 720 730 745 783 870 1003 1020 1032 1140 1266 1305 1340 1472
# 为什么一直 377？
# 245
-> % rg 'I am the leader now.  seq is' `fd metad.log` | grep 'seq is ' | awk '{print $NF}' | sort -n | tr '\n' ' '
609 638 748 760 793 869 1025 1033 1102 1205 1270 1300 1306 1372
# 247
-> % rg 'I am the leader now.  seq is' `fd metad.log` | grep 'seq is ' | awk '{print $NF}' | sort -n | tr '\n' ' '
405 444 526 613 640 660 860 882 1014 1019 1027 1139 1265 1285 1295 1301 1373 1471
```

寻找 seq 交集？无交集

寻找时间交集？

```c++
LOG(INFO) << "Starting the services of meta, this: " << this;
LOG(INFO) << "Done starting the services of meta.";

LOG(INFO) << "Start the follower role.";
LOG(INFO) << "Start the leader role";

LOG(INFO) << "Stopping the services of meta, this: " << this;
LOG(INFO) << "Stopped the services of meta, this: " << this;
```

```bash
lnav filter meta_server.cc
grep 'Starting\|Done starting\|follower role\|leader role\|Stopping\|Stopped' meta.log
```

```bash
# 241
2023-03-14 03:21:19.759223, 2023-03-14 03:21:44.554621, 24, 241
2023-03-14 03:22:48.371652, 2023-03-14 03:24:14.438144, 86, 241
2023-03-14 04:42:07.223759, 2023-03-14 04:42:35.323248, 28, 241
2023-03-14 05:12:11.363139, 2023-03-14 05:12:16.218436, 4, 241
2023-03-14 05:12:22.456939, 2023-03-14 05:12:27.271276, 4, 241
2023-03-14 05:16:09.074290, 2023-03-14 05:16:09.075119, 0, 241
2023-03-14 05:16:10.103627, 2023-03-14 05:16:16.419574, 6, 241
2023-03-14 10:41:37.603965, 2023-03-14 10:42:32.124393, 54, 241
2023-03-14 10:44:55.754539, 2023-03-14 10:45:11.690778, 15, 241
2023-03-14 11:17:00.740545, 2023-03-14 11:17:05.308552, 4, 241
2023-03-14 16:32:01.912210, 2023-03-14 17:16:06.604543, 2644, 241
2023-03-15 00:17:44.369796, 2023-03-15 00:54:24.607848, 2200, 241
2023-03-15 01:59:34.923641, 2023-03-15 02:26:52.072301, 1637, 241
2023-03-15 02:45:57.659147, 2023-03-15 02:46:46.949910, 49, 241
2023-03-16 01:44:41.395365, 2023-03-16 02:19:15.888569, 2074, 241
2023-03-16 02:38:34.145488, 2023-03-16 02:43:25.165120, 291, 241
2023-03-16 02:49:02.491042, 2023-03-16 03:13:08.214232, 1445, 241
2023-03-16 03:51:17.216470, 2023-03-16 04:10:07.399490, 1130, 241
2023-03-16 04:53:13.570667, 2023-03-16 05:03:09.702651, 596, 241
2023-03-16 05:25:29.277910, 2023-03-16 05:50:55.108995, 1525, 241
```

```bash
# 242
2023-03-14 04:53:20.945544, 2023-03-14 05:00:54.235944, 453, 242
2023-03-14 05:10:47.692968, 2023-03-14 05:11:44.572234, 56, 242
2023-03-14 10:15:34.009204, 2023-03-14 10:41:43.030067, 1569, 242
2023-03-14 10:42:57.652433, 2023-03-14 10:43:20.517266, 22, 242
2023-03-14 10:43:39.267174, 2023-03-14 10:44:08.229748, 28, 242
2023-03-14 11:13:54.876852, 2023-03-14 11:16:43.084607, 168, 242
2023-03-14 11:16:50.978901, 2023-03-14 11:16:56.883155, 5, 242
2023-03-15 01:24:35.029403, 2023-03-15 01:48:50.833925, 1455, 242
2023-03-15 02:27:01.056237, 2023-03-15 02:45:55.494180, 1134, 242
2023-03-15 03:51:29.308261, 2023-03-15 04:01:31.112939, 601, 242
2023-03-15 04:27:20.572746, 2023-03-15 18:14:31.940858, 49631, 242
2023-03-16 02:19:17.847744, 2023-03-16 02:33:40.476742, 862, 242
2023-03-16 03:13:10.384635, 2023-03-16 03:51:07.314338, 2276, 242
2023-03-16 04:10:09.513311, 2023-03-16 04:15:13.071264, 303, 242
2023-03-16 05:03:11.834468, 2023-03-16 05:15:09.953123, 718, 242
```

```bash
# 243
2023-03-14 04:46:15.893615, 2023-03-14 04:50:12.341977, 236, 243
2023-03-14 04:51:50.253540, 2023-03-14 04:53:19.598449, 89, 243
2023-03-14 05:11:49.781347, 2023-03-14 05:11:49.819702, 0, 243
2023-03-14 05:11:50.982553, 2023-03-14 05:12:05.629235, 14, 243
2023-03-14 05:12:24.461460, 2023-03-14 05:12:28.821879, 4, 243
2023-03-14 05:12:30.949410, 2023-03-14 05:12:30.960750, 0, 243
2023-03-14 05:12:31.875039, 2023-03-14 05:12:37.557281, 5, 243
2023-03-14 05:13:30.898547, 2023-03-14 05:13:35.423354, 4, 243
2023-03-14 05:15:27.364442, 2023-03-14 05:16:04.411149, 37, 243
2023-03-14 11:38:25.395567, 2023-03-14 16:32:00.248266, 17614, 243
2023-03-14 16:32:11.854652, 2023-03-14 17:16:07.754604, 2635, 243
2023-03-14 17:16:09.751276, 2023-03-14 23:58:24.065742, 24134, 243
2023-03-14 23:58:27.605160, 2023-03-15 00:44:05.055167, 2737, 243
2023-03-15 00:54:26.700101, 2023-03-15 01:13:47.998106, 1161, 243
2023-03-15 01:24:11.591132, 2023-03-15 01:24:28.933976, 17, 243
2023-03-15 02:46:49.267344, 2023-03-15 03:29:31.289547, 2562, 243
2023-03-15 04:26:59.153272, 2023-03-15 04:27:17.006371, 17, 243
2023-03-15 18:18:45.993497, 2023-03-16 01:25:25.469735, 25599, 243
2023-03-16 04:15:14.925086, 2023-03-16 04:47:20.771559, 1925, 243
2023-03-16 05:15:13.245108, 2023-03-16 05:25:17.333786, 604, 243
```

```bash
# 245
2023-03-14 04:10:30.665807, 2023-03-14 04:18:01.016497, 450, 245
2023-03-14 05:12:42.596415, 2023-03-14 05:13:24.182497, 41, 245
2023-03-14 05:13:36.154080, 2023-03-14 05:15:20.262502, 104, 245
2023-03-14 05:16:18.384308, 2023-03-14 05:20:16.125525, 237, 245
2023-03-15 01:48:52.793630, 2023-03-15 01:59:32.484431, 639, 245
2023-03-15 03:29:34.295498, 2023-03-15 03:51:23.793905, 1309, 245
2023-03-15 04:01:33.342466, 2023-03-15 04:01:39.677631, 6, 245
2023-03-15 18:14:38.337919, 2023-03-15 18:17:09.337155, 150, 245
2023-03-16 01:25:27.891759, 2023-03-16 01:44:39.186966, 1151, 245
2023-03-16 03:51:09.149659, 2023-03-16 03:51:12.750885, 3, 245
2023-03-16 04:47:23.495730, 2023-03-16 04:53:13.313684, 349, 245
2023-03-16 05:25:19.608307, 2023-03-16 05:25:20.447633, 0, 245
```

```bash
# 247
2023-03-14 02:59:11.139487, 2023-03-14 03:03:04.297896, 233, 247
2023-03-14 04:08:07.165992, 2023-03-14 04:08:59.370748, 52, 247
2023-03-14 04:18:03.268462, 2023-03-14 04:23:53.477730, 350, 247
2023-03-14 04:41:33.867395, 2023-03-14 04:41:44.585660, 10, 247
2023-03-14 04:42:47.662078, 2023-03-14 04:42:53.222271, 5, 247
2023-03-14 11:16:58.073025, 2023-03-14 11:16:59.344717, 1, 247
2023-03-14 23:58:26.634830, 2023-03-15 00:17:42.196141, 1155, 247
2023-03-15 01:13:54.447059, 2023-03-15 01:18:38.905164, 284, 247
2023-03-15 01:18:48.073276, 2023-03-15 01:24:10.139015, 322, 247
2023-03-15 02:26:54.032968, 2023-03-15 02:26:59.575299, 5, 247
2023-03-15 04:01:45.407310, 2023-03-15 04:26:56.737845, 1511, 247
2023-03-15 18:17:12.604797, 2023-03-15 18:18:42.880754, 90, 247
2023-03-16 02:33:45.452167, 2023-03-16 02:38:29.317943, 283, 247
2023-03-16 02:43:27.317601, 2023-03-16 02:48:58.421901, 331, 247
2023-03-16 03:51:14.093492, 2023-03-16 03:51:16.011716, 1, 247
2023-03-16 05:25:21.804977, 2023-03-16 05:25:25.802548, 3, 247
2023-03-16 05:50:58.611070, 2023-03-16 06:11:20.826815, 1222, 247
```

```bash
2023-03-14 05:12:22.456939, 2023-03-14 05:12:27.271276, 4, 241
2023-03-14 05:12:24.461460, 2023-03-14 05:12:28.821879, 4, 243

2023-03-14 10:15:34.009204, 2023-03-14 10:41:43.030067, 1569, 242
2023-03-14 10:41:37.603965, 2023-03-14 10:42:32.124393, 54, 241

2023-03-14 16:32:01.912210, 2023-03-14 17:16:06.604543, 2644, 241
2023-03-14 16:32:11.854652, 2023-03-14 17:16:07.754604, 2635, 243

2023-03-14 23:58:26.634830, 2023-03-15 00:17:42.196141, 1155, 247
2023-03-14 23:58:27.605160, 2023-03-15 00:44:05.055167, 2737, 243

2023-03-16 02:33:45.452167, 2023-03-16 02:38:29.317943, 283, 247
2023-03-16 02:38:34.145488, 2023-03-16 02:43:25.165120, 291, 241
```

# Case #1

```bash
# 241
I0314 05:12:22.456939 141319 meta_server.cc:408] Start the leader role
I0314 05:12:27.306321 141319 meta_server.cc:521] Stopped the services of meta, this: 0x5629d9056000

# 243
I0314 05:12:24.461460 41094 meta_server.cc:408] Start the leader role
I0314 05:12:28.895712 41094 meta_server.cc:521] Stopped the services of meta, this: 0x55f12cc62000
```

Case #4

```bash
# 247
I0314 23:58:26.040807 134643 quorum_cluster.cc:471] [ROLE CHANGE] I am the leader now.  seq is 882


# 243
I0314 23:58:27.605160 220445 meta_server.cc:408] Start the leader role
I0315 00:44:05.082763 220445 meta_server.cc:521] Stopped the services of meta, this: 0x563dedd9c000
```

# 可疑时间点

```bash
# 1
I0314 04:09:01.782887 Start node242
I0314 04:10:30.665807 Start node245
I0314 04:18:01.016497 Stopping node245
I0314 04:18:03.268462 Start node247
I0314 04:23:52.810838 Stopping node242
I0314 04:23:53.477730 Stopping node247
# 2
I0314 04:30:10.706899 Start node245
I0314 04:41:33.867395 Start node247
I0314 04:41:40.403606 Stopping node245
I0314 04:41:44.585660 Stopping node247
# 3
I0314 04:53:20.945544 Start node242
I0314 05:00:25.366365 Start node243
I0314 05:00:54.235944 Stopping node242
I0314 05:10:41.556015 Stopping node243
# 4
I0314 05:12:22.456939 Start node241
I0314 05:12:24.461460 Start node243
I0314 05:12:27.271276 Stopping node241
I0314 05:12:28.821879 Stopping node243
# 5
I0314 06:05:20.707430 Start node243
I0314 10:15:34.009204 Start node242
I0314 10:15:47.621429 Stopping node243
I0314 10:15:48.118404 Start node243
I0314 10:41:37.603965 Start node241
I0314 10:41:43.030067 Stopping node242
I0314 10:42:32.124393 Stopping node241
# 6
I0314 06:05:20.707430 Start node243
I0314 10:15:34.009204 Start node242
I0314 10:15:47.621429 Stopping node243
I0314 10:15:48.118404 Start node243
I0314 10:41:37.603965 Start node241
I0314 10:41:43.030067 Stopping node242
I0314 10:42:32.124393 Stopping node241
I0314 10:42:57.652433 Start node242
I0314 10:43:20.517266 Stopping node242
I0314 10:43:39.267174 Start node242
I0314 10:44:08.229748 Stopping node242
I0314 10:44:55.754539 Start node241
I0314 10:45:11.690778 Stopping node241
I0314 11:13:54.876852 Start node242
I0314 11:16:43.084607 Stopping node242
I0314 11:16:50.978901 Start node242
I0314 11:16:56.883155 Stopping node242
I0314 11:16:58.073025 Start node247
I0314 11:16:59.344717 Stopping node247
I0314 11:17:00.740545 Start node241
I0314 11:17:05.120203 Stopping node243
I0314 11:17:05.308552 Stopping node241
# 7
I0314 16:32:01.912210 Start node241
I0314 16:32:11.854652 Start node243
I0314 17:16:06.604543 Stopping node241
I0314 17:16:07.754604 Stopping node243
# 8
I0314 23:58:26.634830 Start node247
I0314 23:58:27.605160 Start node243
I0315 00:17:42.196141 Stopping node247
I0315 00:17:44.369796 Start node241
I0315 00:44:05.055167 Stopping node243
I0315 00:54:24.607848 Stopping node241
```

# Case #7

```bash
I0314 16:32:01.912210 Start node241
I0314 16:32:11.854652 Start node243
I0314 17:16:06.604543 Stopping node241
I0314 17:16:07.754604 Stopping node243
```

## node 241 时间线

```bash
I0314 15:58:21.681156 206898 quorum_cluster.cc:482] Leader is 10.0.131.243:10100. seq is 870

# became leader
I0314 16:32:01.479485 206898 quorum_cluster.cc:471] [ROLE CHANGE] I am the leader now.  seq is 871
W0314 16:32:01.479614 206898 quorum_server.cc:82] Role changed.
W0314 16:32:01.479621 206898 quorum_server.cc:104] Reset service.
I0314 16:32:01.517037 206898 meta_server.cc:521] Stopped the services of meta, this: 0x56285dc26000
I0314 16:32:01.517046 206898 meta_server.cc:370] Starting the services of meta, this: 0x56285dc26000
I0314 16:32:01.912767 206898 meta_server.cc:419] Done starting the services of meta.
```

## node 243 时间线

```bash
I0314 11:38:24.969736 39443 quorum_cluster.cc:471] [ROLE CHANGE] I am the leader now.  seq is 870
I0314 11:38:25.395567 39443 meta_server.cc:408] Start the leader role
# ...
# connection timeout
E0314 16:32:00.045940 39441 main.cc:81] [ZOO] handle_socket_error_msg:2393 2023-03-14 16:32:00,045:38748(0x7f70a7ded700):ZOO_ERROR@handle_socket_error_msg│
@2393: Socket [10.0.131.245:2181] zk retcode=-7, errno=110(Connection timed out): connection to 10.0.131.245:2181 timed out (exceeded timeout by 1ms)
# exit leader
I0314 16:32:00.248266 39443 meta_server.cc:495] Stopping the services of meta, this: 0x5652617e6000
# became follower
I0314 16:32:06.489467 177091 meta_server.cc:412] Start the follower role
```

看着不像，暂时搁置。

# Seq 377

## 2023-03-14T01:35:55 之前

```bash
[172_20_131_242_a5fd24ad-03ba-4731-976a-14c5ff4d7f47]/zookeeper.log.1─2023-03-14T01:12:15,664 [myid:] - INFO  [QuorumPeer[myid=2](plain=10.0.131.242:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: leading - broadcast                                   │
# 242 leader, 其他 follower
```

```bash
[172_20_131_242_a5fd24ad-03ba-4731-976a-14c5ff4d7f47]/zookeeper.log.1│2023-03-14T01:35:53,968 [myid:] - INFO  [NIOWorkerThread-4:ZooKeeperServer@828] - Invalid session 0x1000adfaccc0001 for client /10.0.131.243:42672, probably expired
[172_20_131_245_a5fd24ad-03ba-4731-976a-14c5ff4d7f47]/zookeeper.log.1─2023-03-14T01:35:55,330 [myid:] - INFO  [NIOWorkerThread-4:Learner@113] - Revalidating client: 0x1000adfaccc0001
```

## 检查 2023-03-14T01:35:51

该时间点，242 提交了 close session txn

检查各个节点状态：

### 241

```bash
2023-03-14T01:35:47,825 [myid:] - INFO  [QuorumPeer[myid=1](plain=10.0.131.241:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: following - synchronization
2023-03-14T01:35:56,892 [myid:] - INFO  [QuorumPeer[myid=1](plain=10.0.131.241:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: following - broadcast
```

## 242

```bash
2023-03-14T01:35:51,703 [myid:] - INFO  [SessionTracker:ZooKeeperServer@447] - Expiring session 0x1000adfaccc0001, timeout of 4000ms exceeded
2023-03-14T01:35:51,704 [myid:] - INFO  [SessionTracker:QuorumZooKeeperServer@157] - Submitting global closeSession request for session 0x1000adfaccc0001
```

## 243

```bash
2023-03-14T01:35:48,011 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.0.131.243:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: following - synchronization
2023-03-14T01:35:54,812 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.0.131.243:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: following - broadcast
```

## 245

```bash
2023-03-14T01:35:48,450 [myid:] - INFO  [QuorumPeer[myid=4](plain=10.0.131.245:2181)(secure=disabled):QuorumPeer@745] - Peer state changed: following
2023-03-14T01:35:54,474 [myid:] - INFO  [QuorumPeer[myid=4](plain=10.0.131.245:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: following - broadcast
```

## 247

```bash
2023-03-14T01:35:48,442 [myid:] - INFO  [QuorumPeer[myid=5](plain=10.0.131.247:2181)(secure=disabled):QuorumPeer@1345] - FOLLOWING
2023-03-14T01:35:54,472 [myid:] - INFO  [QuorumPeer[myid=5](plain=10.0.131.247:2181)(secure=disabled):QuorumPeer@751] - Peer state changed: following - broadcast
```

疑问：

1. following 在 broadcast 之前的状态，能不能执行 txn？不行。
2. 为什么此时 leader 不会认为 follower 不足，从而退出 leading？推迟调查。
3. 为什么 meta 能 getchildren 拿到 377，但是删不掉？

# 检查奇怪的地方

时间点：2023-03-14T01:35:50 附近

```bash
# 241
2023-03-14T01:35:50,953 [myid:] - INFO  [QuorumPeer[myid=1](plain=10.0.131.241:2181)(secure=disabled):Learner@392] - Getting a diff from the leader 0x100000175e
2023-03-14T01:35:50,975 [myid:] - INFO  [QuorumPeer[myid=1](plain=10.0.131.241:2181)(secure=disabled):QuorumPeer@756] - Peer state changed: following - synchronization - diff
2023-03-14T01:35:51,645 [myid:] - INFO  [QuorumPeer[myid=1](plain=10.0.131.241:2181)(secure=disabled):Learner@552] - Learner received NEWLEADER message


# 243
2023-03-14T01:35:50,980 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.0.131.243:2181)(secure=disabled):Learner@392] - Getting a diff from the leader 0x100000175e
2023-03-14T01:35:51,333 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.0.131.243:2181)(secure=disabled):QuorumPeer@756] - Peer state changed: following - synchronization - diff
2023-03-14T01:35:51,682 [myid:] - WARN  [QuorumPeer[myid=3](plain=10.0.131.243:2181)(secure=disabled):Learner@460] - Got zxid 0x10000016d0 expected 0x1

# 242
2023-03-14T01:35:51,703 [myid:] - INFO  [SessionTracker:ZooKeeperServer@447] - Expiring session 0x1000adfaccc0001, timeout of 4000ms exceeded
2023-03-14T01:35:51,704 [myid:] - INFO  [SessionTracker:QuorumZooKeeperServer@157] - Submitting global closeSession request for session 0x1000adfaccc0001
```

```bash


I0314 01:39:50.133286 109944 main.cc:89] [ZOO] zookeeper_init_internal:1191 2023-03-14 01:39:50,133:109936(0x7fd5b38ff700):ZOO_INFO@zookeeper_init_internal@1191: Initiating client connection, host=10.0.131.241:2181,10.0.131.242:2181,10.0.131.243:2181,10.0.131.245:2181,10.0.131.247:2181 sessionTimeout=4000 watcher=0x55c82b
I0314 01:39:50.157155 110767 quorum_cluster.cc:562] Delete previously joined znode: /zos/service/meta/z-0000000377
I0314 01:39:50.159762 110767 quorum_cluster.cc:584] Joined by creating path: /zos/service/meta/z-0000000395 with value: 10.0.131.243:10100

51 session 过期

```
