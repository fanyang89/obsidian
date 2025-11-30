# 概述

环境：57.62~63
初始：leader=63

```bash
# node62
I0413 16:11:17.547950 11918 quorum_cluster.cc:467] [ROLE CHANGE] I am the leader now.  seq is 680
W0413 16:11:17.548009 11918 quorum_server.cc:104] Reset service.
I0413 16:11:17.565320 11918 meta_server.cc:355] Meta status change to new status: META_STOPPED meta addr: 10.0.57.63 port: 10100. Last status was: META_RUNNING which last for 4655712 ms

I0413 16:11:17.565335 11918 meta_server.cc:355] Meta status change to new status: META_BOOTING meta addr: 10.0.57.63 port: 10100. Last status was: META_STOPPED which last for 0 ms
```

# 结论

配置了 62 优先级

```bash
I0413 16:11:18.198267 11918 quorum_cluster.cc:436]  Found member behind with higher priority, try to rejoin
```

![](assets/Pasted%20image%2020230414133646.png)
