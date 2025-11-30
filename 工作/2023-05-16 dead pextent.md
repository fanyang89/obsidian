```bash
[root@node1-131-241 11:10:02 ~]$zbs-meta pextent find dead
  ID  Replica    Alive Replica    Temporary Replica    Ever Exist    Is Garbage      Origin PExtent    Expected Replica num    Epoch    Prefer Local  Allocated Space
----  ---------  ---------------  -------------------  ------------  ------------  ----------------  ----------------------  -------  --------------  -----------------
1105  [5, 1, 3]  []               []                   True          False                        0                       3     1401               5  0.00 B

[root@node1-131-241 11:10:07 ~]$zbs-meta pextent getref 1105
-----------------------  ---------------------------------------
Pool Name                b'prometheus'
Volume ID                b'd80753b1-de05-427f-8568-7f10de9dde38'
Volume Name              b'd80753b1-de05-427f'
Volume Is Snapshot       False
Volume Snapshot Pool ID  b''
Snapshot Name            b''
-----------------------  ---------------------------------------

[root@node1-131-241 11:11:35 ~]$zbs-meta chunk list
  ID  IP            Host Name        Port  Storage Pool    Use State    Link Status        Data Capacity    Used Space    Provisioned Space    Allocated Space    Failure Data Space    Failure Cache Space    Cache Capacity    Used Cache Space    Registered Date      LSM Version    Zone     Maintenance Mode
----  ------------  -------------  ------  --------------  -----------  -----------------  ---------------  ------------  -------------------  -----------------  --------------------  ---------------------  ----------------  ------------------  -------------------  -------------  -------  ------------------
   1  10.0.131.244  node4-131-244   10200  system          IN_USE       CONNECTED_HEALTHY  188.23 GiB       75.75 GiB     75.75 GiB            33.99 GiB          0.00 B                0.00 B                 350.00 GiB        25.08 GiB           2023-05-10 19:32:25  2.3.0          default  False
   2  10.0.131.241  node1-131-241   10200  system          IN_USE       CONNECTED_HEALTHY  188.23 GiB       17.25 GiB     17.25 GiB            2.61 GiB           0.00 B                0.00 B                 350.00 GiB        2.61 GiB            2023-05-10 19:32:25  2.3.0          default  False
   3  10.0.131.243  node3-131-243   10200  system          IN_USE       CONNECTED_HEALTHY  188.23 GiB       25.25 GiB     25.25 GiB            9.11 GiB           0.00 B                0.00 B                 350.00 GiB        9.11 GiB            2023-05-10 19:32:25  2.3.0          default  False
   4  10.0.131.242  node2-131-242   10200  system          IN_USE       CONNECTED_HEALTHY  188.23 GiB       8.00 GiB      8.00 GiB             3.52 GiB           0.00 B                0.00 B                 350.00 GiB        3.52 GiB            2023-05-10 19:32:25  2.3.0          default  False
   5  10.0.131.245  node5-131-245   10200  system          IN_USE       CONNECTED_HEALTHY  188.23 GiB       78.25 GiB     78.25 GiB            33.98 GiB          0.00 B                0.00 B                 350.00 GiB        24.04 GiB           2023-05-10 19:32:25  2.3.0          default  False
```

```
I0511 11:08:47.465557 30781 generation_syncor.cc:129] [SYNC GENERATION START] pid: 1109 loc: [5 1 2 ]
I0511 11:08:47.465620 26465 lsm.cc:4786] [ALLOC EXTENT] status: EXTENT_STATUS_ALLOCATED pid: 1109 epoch: 1402 generation: 0 bucket_id: 1109 einode_id: 4195254 thick_provision: false
I0511 11:08:47.465200 24231 lsm.cc:4786] [ALLOC EXTENT] status: EXTENT_STATUS_ALLOCATED pid: 1109 epoch: 1402 generation: 0 bucket_id: 1109 einode_id: 4195185 thick_provision: false
I0511 11:08:47.466004 30783 lsm.cc:4786] [ALLOC EXTENT] status: EXTENT_STATUS_ALLOCATED pid: 1109 epoch: 1402 generation: 0 bucket_id: 1109 einode_id: 4194547 thick_provision: false

I0511 11:08:47.470204 30781 generation_syncor.cc:220] [SYNC GENERATION SUCCESS]  pid: 1109 loc: [5 1 2 ] gen: 0 io_hard: 0
I0511 11:08:47.489995 30781 access_io_handler.cc:1625] [SET PEXTENT EXISTENCE]: pid: 1109 wctx: volume_id: "94696d39-1b31-4593-9280-abea3ad43c65", vextent no: 0, data_len: 7525, extent_offset: 0, flags: 0, gen: 0, loc: 131333  st: OK

I0511 11:06:14.813447 165867 nfs_server.cc:954] [RENAME INODE]: [REQUEST]:
from_parent_id: "0b92c7ac-975e-4c11" from_name: "000032.tmp"
to_parent_id: "0b92c7ac-975e-4c11" to_name: "000032" xid: 4291179676,
[RESPONSE]: ST:OK, inode { name: "000032" pool_id: "f2d34913-ccca-427d-bbbb-e191a66b573b" id: "d80753b1-de05-427f" parent_id: "0b92c7ac-975e-4c11" attr { type: FILE mode: 420 uid: 0 gid: 0 size: 131072 ctime { seconds: 1683774370 nseconds: 927732427 } mtime { seconds: 1683774370 nseconds: 927732427 } atime { seconds: 1683774370 nseconds: 784390324 } } volume_id: "d80753b1-de05-427f-8568-7f10de9dde38" xid: 4291179676 }, [TIME]: 643287 us.
```

```
# chunk
# 10_0_131_245 zbs-chunkd.log.20230510-193215.30777
I0511 11:34:09.065771 30781 generation_syncor.cc:129] [SYNC GENERATION START] pid: 1105 loc: [5 1 3 ]

# meta
I0511 11:34:11.585657 175956 pextent_table_entry.cc:94] [IGNORE SMALL GEN]: wrong gen: 0 expected gen: 1 pid: 1105 now_ms: 58463200 cid: 5
```

![](assets/Pasted%20image%2020230516134824.png)

检查 sync gen 的发起者：241 245
