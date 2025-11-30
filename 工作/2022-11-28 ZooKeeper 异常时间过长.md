# 测试脚本日志

```bash
11/28/22-16:05:57 [Thread-69               ] [INFO    ] [modules.utils: 54] running cmd 'nohup fio /tmp/20221128160536/832178a4-94c4-4443-b1e6-49919f432261-0.conf --output-format=json --write_iops_log=/tmp/20221128160536/832178a4-94c4-4443-b1e6-49919f432261-0 --write_bw_log=/tmp/20221128160536/832178a4-94c4-4443-b1e6-49919f432261-0 --write_lat_log=/tmp/20221128160536/832178a4-94c4-4443-b1e6-49919f432261-0 > /dev/null 2>&1' on host '172.20.130.110'
11/28/22-16:07:11 [Thread-78               ] [INFO    ] [modules.utils: 54] running cmd 'sudo ifdown eth1' on host '172.20.130.66'
11/28/22-16:08:32 [Thread-83               ] [INFO    ] [modules.utils: 54] running cmd 'sudo ifdown eth1' on host '172.20.130.67'

11/28/22-16:09:47 [Thread-89               ] [INFO    ] [modules.utils: 54] running cmd 'sudo ifup eth1' on host '172.20.130.67'
11/28/22-16:09:47 [Thread-88               ] [INFO    ] [modules.utils: 54] running cmd 'sudo ifup eth1' on host '172.20.130.66'
```

# 时间线

16:07:11 Node66 断网
16:08:32 Node67 断网

16:09:47 Node{66,67} 恢复
恢复时间点？

Node66

```
2022-11-28T16:10:04,199 [myid:] - INFO  [QuorumPeer[myid=2](plain=10.20.130.66:2181)(secure=disabled):QuorumPeer@745] - Peer state changed: following
```

Node67

```
2022-11-28T16:10:36,973 [myid:] - INFO  [QuorumPeer[myid=3](plain=10.20.130.67:2181)(secure=disabled):QuorumPeer@745] - Peer state changed: leading
```
