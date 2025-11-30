```
I1207 12:59:12.467963 171271 chunk_manager.cc:102] Done initializing ChunkManager.
I1207 12:59:12.468103 171271 meta_rpc_server.cc:121] Initializing MetaRpcServer
I1207 12:59:12.468135 171271 meta_rpc_server.cc:135] Done initializing MetaRpcServer as follower
I1207 12:59:12.468151 171271 meta_context.cc:285] Done initializing meta context as follower
I1207 12:59:12.468174 171271 meta_server.cc:396] [MetaServer] initializing meta context done, this: 0x55dbd7dae000, status: OK
I1207 12:59:12.469753 171377 thread.cc:114] Set thread name to: stat-server
I1207 12:59:12.469794 171377 coroutine.cc:448] Adjust max cached coroutines for current thread,  old: 64 new:64 current:0
I1207 12:59:12.470736 171271 meta_server.cc:234] Status Server starts
I1207 12:59:12.470785 171271 meta_server.cc:178] Start meta rpc server.
I1207 12:59:12.470952 171268 meta_server.cc:316] Meta rpc server starts at 10.0.130.103:10100
I1207 12:59:12.471099 171271 meta_server.cc:184] Start meta rpc server done.
I1207 12:59:12.471302 171271 meta_server.cc:353] Meta status change to new status: META_RUNNING meta addr: 10.0.130.103 port: 10100. Last status was: META_BOOTING which last for 838 ms
I1207 12:59:12.472218 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority
I1207 12:59:12.475217 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority/current
I1207 12:59:12.475246 171271 meta_server.cc:412] Done starting the services of meta.
I1207 12:59:12.475275 171271 quorum_server.cc:71] Get quorum cluster event: Members_Changed
I1207 12:59:12.475298 171271 quorum_server.cc:90] Members changed.
< --- here
I1207 13:38:48.294375 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority
I1207 13:38:48.295116 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority/current
I1207 13:38:48.295146 171271 quorum_cluster.cc:477] Leader is 10.0.130.102:10100. seq is 48
I1207 13:38:48.295164 171271 quorum_cluster.cc:498] [MEMBER IDX]: 1
I1207 13:38:48.295193 171271 quorum_server.cc:71] Get quorum cluster event: Members_Changed
I1207 13:38:48.295210 171271 quorum_server.cc:90] Members changed.
I1207 13:38:49.137970 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority
I1207 13:38:49.141172 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority/current
I1207 13:38:49.141212 171271 quorum_cluster.cc:477] Leader is 10.0.130.102:10100. seq is 48
I1207 13:38:49.141253 171271 quorum_server.cc:71] Get quorum cluster event: Members_Changed
I1207 13:38:49.141270 171271 quorum_server.cc:90] Members changed.
I1207 13:38:49.360296 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority
I1207 13:38:49.360961 171271 quorum_cluster.cc:659] Get priority string '' from /zos/service/meta_priority/current
I1207 13:38:49.360994 171271 quorum_cluster.cc:466] [ROLE CHANGE] I am the leader now.  seq is 49
I1207 13:38:49.361027 171271 quorum_server.cc:71] Get quorum cluster event: ROLE_CHANGED
W1207 13:38:49.361043 171271 quorum_server.cc:79] Role changed.
W1207 13:38:49.361059 171271 quorum_server.cc:101] Reset service.
I1207 13:38:49.361075 171271 meta_server.cc:488] Stopping the services of meta, this: 0x55dbd7dae000
I1207 13:38:49.361092 171271 meta_server.cc:484] PrometheusHTTPHandlers' response has been forcefully disabled.
I1207 13:38:49.361192 171271 meta_server.cc:250] Join status server thread.
I1207 13:38:49.361722 171271 meta_server.cc:255] Status Server exits
I1207 13:38:49.361794 171268 meta_rpc_server.cc:143] Stop the MetaRpcServer
I1207 13:38:49.361815 171268 meta_rpc_server.cc:154] Stop the MetaRpcServer done
```

![](assets/Pasted%20image%2020231207161540.png)

```
2023-12-07T05:04:55.159Z In(182) vmkernel: cpu10:2099084 opID=a9f5cca4)SunRPC: 1096: Destroying world 0x22222f
2023-12-07T05:04:55.160Z In(182) vmkernel: cpu10:2099084 opID=a9f5cca4)SunRPC: 1096: Destroying world 0x222230
2023-12-07T05:04:55.161Z In(182) vmkernel: cpu10:2099084 opID=a9f5cca4)SunRPC: 1096: Destroying world 0x222231
2023-12-07T05:04:55.162Z In(182) vmkernel: cpu10:2099084 opID=a9f5cca4)NFS: 340: Restored connection to the server 192.168.33.2 mount point /zbs/fi-0753971d-bf82-42f2-a175-d2ac9f412633, mounted as f0102e1f-41b7b812-0000-000000000000 ("fi-0753971d-bf82-42f2-a175-d2ac9f412$
2023-12-07T05:04:55.162Z In(182) vmkernel: cpu10:2099084 opID=a9f5cca4)NFS: 317: NFS mount succeeded for 192.168.33.2:/zbs/fi-0753971d-bf82-42f2-a175-d2ac9f412633 volume fi-0753971d-bf82-42f2-a175-d2ac9f412633.
2023-12-07T05:05:16.618Z In(182) vmkernel: cpu0:2127075 opID=55d67df2)World: 12231: VC opID vim-cmd-68-dba4 maps to vmkernel opID 55d67df2
2023-12-07T05:05:16.618Z In(182) vmkernel: cpu0:2127075 opID=55d67df2)VSCSI: 5253: handle 9135781989851158(GID:8214)(vscsi0:2):Creating Virtual Device for world 2127090 (FSS handle 6706816) numBlocks=20971520 (bs=512)
2023-12-07T05:05:16.618Z In(182) vmkernel: cpu0:2127075 opID=55d67df2)VSCSI: 272: handle 9135781989851158(GID:8214)(vscsi0:2):Input values: res=0 limit=-1 bw=-1 Shares=-1
2023-12-07T05:05:16.639Z In(182) vmkernel: cpu7:2127094)VSCSIFs: 4390: handle 9135781989851158(GID:8214)(vscsi0:2):Invalid Opcode (0xa3) from (vmm0:testvm02)

2023-12-07T05:06:49.241Z In(182) vmkernel: cpu7:2097365)NFS: 6339: Status:No connection. Retrying synchronous write I/O 1 of 25 times
2023-12-07T05:06:51.069Z In(182) vmkernel: cpu0:2238618)Tcpip: 1414: delete dstAddr=0x221a8c0, netMask=0xffffffff, gwAddr=0x6682000a ifName=0x0x0, ifNameLen=0
2023-12-07T05:06:51.213Z In(182) vmkernel: cpu0:2238622)Tcpip: 1414: add dstAddr=0x221a8c0, netMask=0xffffffff, gwAddr=0x6582000a ifName=0x0x0, ifNameLen=0
2023-12-07T05:07:04.241Z In(182) vmkernel: cpu3:2097803)NFSLock: 1505: Stop accessing fd 0x43088f61ece0(fi-0753971d-bf82-42f2-a175-d2ac9f412633-1-flat.vmdk)  3
2023-12-07T05:07:09.800Z In(182) vmkernel: cpu3:2097803)NFSLock: 1469: Start accessing fd 0x43088f61ece0(fi-0753971d-bf82-42f2-a175-d2ac9f412633-1-flat.vmdk) again
2023-12-07T05:07:09.945Z Wa(180) vmkwarning: cpu3:2097803)WARNING: NFS: 5544: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 performance has deteriorated. I/O latency increased from average value of 0(us) to 10614(us). Exceeded threshold 10000(us)
2023-12-07T05:07:10.416Z Wa(180) vmkwarning: cpu2:2097803)WARNING: NFS: 5544: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 performance has deteriorated. I/O latency increased from average value of 10614(us) to 21639(us). Exceeded threshold 10000(us)
2023-12-07T05:07:24.239Z In(182) vmkernel: cpu1:2097803)NFSLock: 1505: Stop accessing fd 0x43088f604dd0(vmx-testvm02-1efc878d81c8d6ddc0fbb6316dd103c6b8694c46dc8b53f087e838a368b0d281-1.vswp)  3
2023-12-07T05:07:24.239Z In(182) vmkernel: cpu1:2097803)NFSLock: 1505: Stop accessing fd 0x43088f419390(testvm02.vmx.lck)  3
2023-12-07T05:07:24.239Z In(182) vmkernel: cpu1:2097803)NFSLock: 1505: Stop accessing fd 0x43088f40a7f0(testvm02-c0b8fd01.vswp)  3
2023-12-07T05:07:24.239Z In(182) vmkernel: cpu1:2097803)NFSLock: 1505: Stop accessing fd 0x43088f41dde0(CentOS-7-x86_64-Minimal-2003)  3
2023-12-07T05:07:24.239Z In(182) vmkernel: cpu1:2097803)NFSLock: 1505: Stop accessing fd 0x43088f601770(testvm02-flat.vmdk)  3

2023-12-07T05:07:33.489Z In(182) vmkernel: cpu7:2097365)NFS: 6339: Status:No connection. Retrying synchronous write I/O 1 of 25 times
2023-12-07T05:07:33.535Z In(182) vmkernel: cpu1:2097803)NFSLock: 1469: Start accessing fd 0x43088f41dde0(CentOS-7-x86_64-Minimal-2003) again
2023-12-07T05:07:34.033Z In(182) vmkernel: cpu1:2097803)NFSLock: 1469: Start accessing fd 0x43088f604dd0(vmx-testvm02-1efc878d81c8d6ddc0fbb6316dd103c6b8694c46dc8b53f087e838a368b0d281-1.vswp) again
2023-12-07T05:07:34.039Z In(182) vmkernel: cpu1:2097803)NFSLock: 1469: Start accessing fd 0x43088f601770(testvm02-flat.vmdk) again
2023-12-07T05:07:34.039Z In(182) vmkernel: cpu1:2097803)NFSLock: 1469: Start accessing fd 0x43088f419390(testvm02.vmx.lck) again
2023-12-07T05:07:34.039Z In(182) vmkernel: cpu1:2097803)NFSLock: 1469: Start accessing fd 0x43088f40a7f0(testvm02-c0b8fd01.vswp) again
2023-12-07T05:07:43.348Z Wa(180) vmkwarning: cpu2:2097803)WARNING: NFS: 5575: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 average I/O latency 21336(us) has exceeded threshold 10000(us) for last 10 minutes
2023-12-07T05:08:21.439Z Wa(180) vmkwarning: cpu8:2097803)WARNING: NFS: 5544: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 performance has deteriorated. I/O latency increased from average value of 21639(us) to 55609(us). Exceeded threshold 10000(us)
2023-12-07T05:08:21.489Z Wa(180) vmkwarning: cpu8:2097803)WARNING: NFS: 5506: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 performance has deteriorated. I/O latency increased from average value of 11405(us) to 117255(us).
2023-12-07T05:08:23.854Z Wa(180) vmkwarning: cpu7:2097803)WARNING: NFS: 5506: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 performance has deteriorated. I/O latency increased from average value of 11746(us) to 120169(us).
2023-12-07T05:08:25.826Z Wa(180) vmkwarning: cpu9:2097803)WARNING: NFS: 5506: NFS volume fi-0753971d-bf82-42f2-a175-d2ac9f412633 performance has deteriorated. I/O latency increased from average value of 12019(us) to 172375(us).
```

```
I1207 13:07:06.134732  2336 tcp_zbs_transport.cc:363] [NFS] client login:  10.0.130.112:858 fd: 134
I1207 13:07:33.503747  2336 tcp_zbs_transport.cc:363] [NFS] client login:  10.0.130.112:859 fd: 111
```

![](assets/Pasted%20image%2020231207201257.png)

cluster-logs-2023120712-2023120714-b71c45ff.zip
