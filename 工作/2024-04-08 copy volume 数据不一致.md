```
[root@node-128-76 14:18:05 smartx]$ zbs-meta volume show zbs-iscsi-datastore-1704966868820a fcc387c9-0f16-4071-99e9-969114efb174 --show_replica_distribution
-----------------  ------------------------------------
ID                 fcc387c9-0f16-4071-99e9-969114efb174
Name               fcc387c9-0f16-4071-99e9-969114efb174
Parent ID          e0fc49bb-b19c-4c8f-a519-75bc803e4614

$ zbs-meta volume show_by_id e0fc49bb-b19c-4c8f-a519-75bc803e4614
2024-04-11 17:31:32,360, ERROR, cmd.py:3599, Traceback:
[EMetaCorrupt] Volume's property is corrupt

Size               20.00 GiB
Perf Unique Size   0.00 B
Perf Shared Size   0.00 B
Cap Unique Size    0.00 B
Cap Shared Size    5.47 GiB
Logical Used Size  2.73 GiB
Creation Time      2024-02-04 19:43:40.72484941
Status             VOLUME_ONLINE
Resiliency Type    RT_REPLICA
Replica Num        2
EC Param
Thin Provision     True
Read Only          False
Description
Alloc Even         False
Clone count        4
Iops               0
Iops Read          0
Iops Write         0
Bps                0
Bps Read           0
Bps Write          0
Stripe Num         4
Stripe Size        262144
Prefer CID         0
Access Points      []
Is Prioritized     False
Downgraded PRS     0.00 B
-----------------  ------------------------------------
  chunk_id    total_replica_count    alive_replica_count    prefer_local_count    lease_owner_count
----------  ---------------------  ---------------------  --------------------  -------------------
         0                      0                      0                    80                  149
         3                      6                      6                     0                    0
         4                     22                     22                     6                    9
         1                     74                     74                    16                    0
         2                     58                     58                    58                    2
```

```
[root@node-128-76 14:19:17 smartx]$ zbs-meta volume show zbs-iscsi-datastore-1704966868820a fcc387c9-0f16-4071-99e9-969114efb174 --show_pextents
-----------------  ------------------------------------
ID                 fcc387c9-0f16-4071-99e9-969114efb174
Name               fcc387c9-0f16-4071-99e9-969114efb174
Parent ID          e0fc49bb-b19c-4c8f-a519-75bc803e4614
Size               20.00 GiB
Perf Unique Size   0.00 B
Perf Shared Size   0.00 B
Cap Unique Size    0.00 B
Cap Shared Size    5.47 GiB
Logical Used Size  2.73 GiB
Creation Time      2024-02-04 19:43:40.72484941
Status             VOLUME_ONLINE
Resiliency Type    RT_REPLICA
Replica Num        2
EC Param
Thin Provision     True
Read Only          False
Description
Alloc Even         False
Clone count        4
Iops               0
Iops Read          0
Iops Write         0
Bps                0
Bps Read           0
Bps Write          0
Stripe Num         4
Stripe Size        262144
Prefer CID         0
Access Points      []
Is Prioritized     False
Downgraded PRS     0.00 B
-----------------  ------------------------------------
PExtent Type         ID  Replica    Alive Replica    Temporary Replica    Ever Exist    Is Garbage      Origin PExtent    Origin Epoch    Expected Replica num    Epoch    Prefer Local    Lease Owner  Allocated Space    Is Prioritized    Is Sinkable
--------------  -------  ---------  ---------------  -------------------  ------------  ------------  ----------------  --------------  ----------------------  -------  --------------  -------------  -----------------  ----------------  -------------
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_PERF               0  []         []               []                   False         False                        0               0                       0        0               0              0  0.00 B             False             False
PT_CAP          2722707  [3, 4]     [3, 4]           []                   True          False                  1190298         1308058                       2  2911990               4              0  63.00 MiB          False
PT_CAP          2722709  [4, 1]     [4, 1]           []                   True          False                  1190299         1308059                       2  2911992               1              0  86.25 MiB          False
PT_CAP          2722703  [3, 4]     [3, 4]           []                   True          False                  1190300         1308060                       2  2911986               4              0  61.00 MiB          False
PT_CAP          2722705  [4, 1]     [4, 1]           []                   True          False                  1190301         1308061                       2  2911988               1              0  85.50 MiB          False
PT_CAP          1190302  [2, 1]     [2, 1]           []                   True          False                        0               0                       2  1308062               2              0  511.50 MiB         False
PT_CAP          1190303  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308063               2              0  0.00 B             False
PT_CAP          1190304  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308064               2              0  0.00 B             False
PT_CAP          1190305  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308065               2              0  0.00 B             False
PT_CAP          1190306  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308066               2              0  0.00 B             False
PT_CAP          1190307  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308067               2              0  0.00 B             False
PT_CAP          1190308  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308068               2              0  0.00 B             False
PT_CAP          1190309  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308069               2              0  0.00 B             False
PT_CAP          2722711  [3, 4]     [3, 4]           []                   True          False                  1190310         1308070                       2  2911994               4              0  256.00 MiB         False
PT_CAP          2722722  [2, 1]     [2, 1]           []                   True          False                  1190311         1308071                       2  2912005               2              2  511.00 MiB         False
PT_CAP          2722726  [4, 1]     [4, 1]           []                   True          False                  1190312         1308072                       2  2912009               1              4  382.75 MiB         False
PT_CAP          2722724  [4, 1]     [4, 1]           []                   True          False                  1190313         1308073                       2  2912007               1              4  382.75 MiB         False
PT_CAP          1190314  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308074               2              0  0.00 B             False
PT_CAP          1190315  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308075               2              0  0.00 B             False
PT_CAP          1190316  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308076               2              0  0.00 B             False
PT_CAP          1190317  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308077               2              0  0.00 B             False
PT_CAP          1190318  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308078               2              0  0.00 B             False
PT_CAP          1190319  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308079               2              0  0.00 B             False
PT_CAP          1190320  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308080               2              0  0.00 B             False
PT_CAP          1190321  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308081               2              0  0.00 B             False
PT_CAP          1190322  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308082               2              0  0.00 B             False
PT_CAP          1190323  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308083               2              0  0.00 B             False
PT_CAP          1190324  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308084               2              0  0.00 B             False
PT_CAP          1190325  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308085               2              0  0.00 B             False
PT_CAP          2722728  [3, 4]     [3, 4]           []                   True          False                  1190326         1308086                       2  2912011               4              0  151.00 MiB         False
PT_CAP          2722730  [4, 3]     [4, 3]           []                   True          False                  1190327         1308087                       2  2912013               4              4  150.50 MiB         False
PT_CAP          2722713  [4, 1]     [4, 1]           []                   True          False                  1190328         1308088                       2  2911996               1              0  266.75 MiB         False
PT_CAP          2722717  [2, 1]     [2, 1]           []                   True          False                  1190329         1308089                       2  2912000               2              2  383.50 MiB         False
PT_CAP          1190330  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308090               2              0  0.00 B             False
PT_CAP          1190331  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308091               2              0  0.00 B             False
PT_CAP          1190332  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308092               2              0  0.00 B             False
PT_CAP          1190333  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308093               2              0  0.00 B             False
PT_CAP          1190334  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308094               2              0  0.00 B             False
PT_CAP          1190335  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308095               2              0  0.00 B             False
PT_CAP          1190336  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308096               2              0  0.00 B             False
PT_CAP          1190337  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308097               2              0  0.00 B             False
PT_CAP          1190338  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308098               2              0  0.00 B             False
PT_CAP          1190339  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308099               2              0  0.00 B             False
PT_CAP          1190340  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308100               2              0  0.00 B             False
PT_CAP          1190341  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308101               2              0  0.00 B             False
PT_CAP          2722699  [4, 1]     [4, 1]           []                   True          False                  1190342         1308102                       2  2911982               1              0  214.25 MiB         False
PT_CAP          2722701  [4, 1]     [4, 1]           []                   True          False                  1190343         1308103                       2  2911984               1              0  215.50 MiB         False
PT_CAP          2722695  [4, 1]     [4, 1]           []                   True          False                  1190344         1308104                       2  2911978               1              0  214.25 MiB         False
PT_CAP          2722697  [4, 1]     [4, 1]           []                   True          False                  1190345         1308105                       2  2911980               1              0  214.25 MiB         False
PT_CAP          1190346  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308106               2              0  0.00 B             False
PT_CAP          1190347  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308107               2              0  0.00 B             False
PT_CAP          1190348  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308108               2              0  0.00 B             False
PT_CAP          1190349  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308109               2              0  0.00 B             False
PT_CAP          1190350  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308110               2              0  0.00 B             False
PT_CAP          1190351  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308111               2              0  0.00 B             False
PT_CAP          1190352  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308112               2              0  0.00 B             False
PT_CAP          1190353  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308113               2              0  0.00 B             False
PT_CAP          1190354  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308114               2              0  0.00 B             False
PT_CAP          1190355  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308115               2              0  0.00 B             False
PT_CAP          1190356  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308116               2              0  0.00 B             False
PT_CAP          1190357  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308117               2              0  0.00 B             False
PT_CAP          2722715  [4, 1]     [4, 1]           []                   True          False                  1190358         1308118                       2  2911998               1              4  127.00 MiB         False
PT_CAP          2722732  [4, 1]     [4, 1]           []                   True          False                  1190359         1308119                       2  2912015               1              4  126.00 MiB         False
PT_CAP          2722734  [4, 1]     [4, 1]           []                   True          False                  1190360         1308120                       2  2912017               1              4  126.00 MiB         False
PT_CAP          2722736  [4, 1]     [4, 1]           []                   True          False                  1190361         1308121                       2  2912019               1              4  126.00 MiB         False
PT_CAP          2722738  [3, 4]     [3, 4]           []                   True          False                  1190362         1308122                       2  2912021               4              0  70.50 MiB          False
PT_CAP          2725306  [4, 1]     [4, 1]           []                   True          False                  1190363         1308123                       2  2914589               1              4  291.25 MiB         False
PT_CAP          2722740  [4, 1]     [4, 1]           []                   True          False                  1190364         1308124                       2  2912023               1              0  291.25 MiB         False
PT_CAP          2722742  [4, 1]     [4, 1]           []                   True          False                  1190365         1308125                       2  2912025               1              4  291.25 MiB         False
PT_CAP          1190366  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308126               2              0  0.00 B             False
PT_CAP          1190367  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308127               2              0  0.00 B             False
PT_CAP          1190368  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308128               2              0  0.00 B             False
PT_CAP          1190369  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308129               2              0  0.00 B             False
PT_CAP          1190370  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308130               2              0  0.00 B             False
PT_CAP          1190371  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308131               2              0  0.00 B             False
PT_CAP          1190372  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308132               2              0  0.00 B             False
PT_CAP          1190373  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308133               2              0  0.00 B             False
PT_CAP          1190374  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308134               2              0  0.00 B             False
PT_CAP          1190375  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308135               2              0  0.00 B             False
PT_CAP          1190376  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308136               2              0  0.00 B             False
PT_CAP          1190377  [2, 1]     [2, 1]           []                   False         False                        0               0                       2  1308137               2              0  0.00 B             False
TEMPORARY REPLICA NUM: 0
```

```bash
grep -ri 'data not equal' /var/log/zbs/zbs-chunkd.*s

# verify src vol
zbs-chunk volume read --no-promotion --ignore_zero zbs-iscsi-datastore-1704966868820a fcc387c9-0f16-4071-99e9-969114efb174 | md5sum -b
# 84199edeee47eefd0be602ac87a4a4fd *- , it's correct

# verify dst vol
zbs-chunk volume read --no-promotion --ignore_zero fanyang-test fanyang-test-vol1 | md5sum -b
# 9196036aacd13478c2355d6d2a90aa2e *-

-> % ./zbs-dev-tools volume checksum --hash md5 --file ~/tmp/task-copy-volume-diff/vdd.bin
19.96 GiB / 20.00 GiB [==================================================================>] 100 % 691.53 MiB/s
MD5: 84199edeee47eefd0be602ac87a4a4fd

-> % ./zbs-dev-tools volume checksum --file ~/tmp/task-copy-volume-diff/vdd.bin
19.31 GiB / 20.00 GiB [===================================================================>--] 97 % 5.36 GiB/s
xxhash: af7eba5b7c5375cd

-> % ./zbs-dev-tools volume checksum --file ~/tmp/task-copy-volume-diff/vdf.bin
19.78 GiB / 20.00 GiB [====================================================================>-] 99 % 1.91 GiB/s
xxhash: 1f7b222bba4b22d7

zbs-task copy_volume copy fcc387c9-0f16-4071-99e9-969114efb174 9c199cfa-e53c-4a73-b853-974d35b380f2 \
10.0.18.215:10201:10206,10.0.18.216:10201:10206,10.0.18.217:10201:10206,10.0.18.218:10201:10206 \
10.0.18.215:10201:10206,10.0.18.216:10201:10206,10.0.18.217:10201:10206,10.0.18.218:10201:10206

zbs-task copy_volume copy fcc387c9-0f16-4071-99e9-969114efb174 9c199cfa-e53c-4a73-b853-974d35b380f2 \
10.0.18.215:10201:10206 10.0.18.215:10201:10206
# task id: e17cf8b0-86f0-42d5-a7ac-09809f97b81f
zbs-task task show e17cf8b0-86f0-42d5-a7ac-09809f97b81f | grep percentage
# 9196036aacd13478c2355d6d2a90aa2e *-, it's wrong

zbs-task copy_volume copy fcc387c9-0f16-4071-99e9-969114efb174 9c199cfa-e53c-4a73-b853-974d35b380f2 \
10.0.18.216:10201:10206 10.0.18.216:10201:10206
# task id: 6d657d29-50f6-41cd-9441-200812609bfa
zbs-task task show 6d657d29-50f6-41cd-9441-200812609bfa | grep percentage
# 9196036aacd13478c2355d6d2a90aa2e *-

zbs-task copy_volume copy fcc387c9-0f16-4071-99e9-969114efb174 e9581579-8ee7-4303-897d-a634b787c994 \
10.0.18.215:10201:10206 10.0.18.215:10201:10206

zbs-task copy_volume copy fcc387c9-0f16-4071-99e9-969114efb174 9c199cfa-e53c-4a73-b853-974d35b380f2 \
10.0.18.216:10201:10206 10.0.18.216:10201:10206
# bf736922-1741-4ee7-89c6-f0e59de19c79
zbs-task task show bf736922-1741-4ee7-89c6-f0e59de19c79 | grep percentage
# 9196036aacd13478c2355d6d2a90aa2e *-

# with patch 1
zbs-task copy_volume copy fcc387c9-0f16-4071-99e9-969114efb174 9c199cfa-e53c-4a73-b853-974d35b380f2 \
10.0.18.216:10201:10206
#  'id': '21eab289-105f-4adc-8a51-f9c90f035738',
```

# 怀疑 libzbs 有问题

```bash
# zbs-chunk volume write default v1 -i ./task-copy-volume-diff/vdd.bin

# zbs-chunk volume read default v1 | md5sum -b
8d1993a98fd92f1bbf987752f3fc33a2 *-

default/v1 100% |##################################| 435.2 MiB/s Time:  0:00:47
8d1993a98fd92f1bbf987752f3fc33a2 *-

default/v1 100% |##################################| 435.2 MiB/s Time:  0:00:47
8d1993a98fd92f1bbf987752f3fc33a2 *-

# md5sum -b ./task-copy-volume-diff/vdd.bin
84199edeee47eefd0be602ac87a4a4fd *./task-copy-volume-diff/vdd.bin
```

```bash
# go run main.go metadb query -c zbs.DataComparator --keyword e0fc49bb-b19c-4c8f-a519-75bc803e4614 --key-display none --key-format string --value-hash xxh --find-prefix
Prefix: [
  "/1/e0fc49bb-b19c-4c8f-a519-75bc803e4614",
  "/e0fc49bb-b19c-4c8f-a519-75bc803e4614",
  "/ie0fc49bb-b19c-4c8f-a519-75bc803e4614"
]
```

```
[root@node-128-75 16:52:41 fanyang]$ ./zbs-dev-tools meta volume show --meta-addr 10.0.18.216:10100 --volume-id fcc387c9-0f16-4071-99e9-969114efb174 | ./zbs-dev-tools json query --query 'volume'
{
  "access_points": [],
  "bps_burst": "0",
  "clone_count": "4",
  "created_time": {
    "nseconds": "72484941",
    "seconds": "1707047020"
  },
  "downgraded_prioritized_space": "0",
  "id": "fcc387c9-0f16-4071-99e9-969114efb174",
  "iops_burst": "0",
  "logical_used_size": "2935488512",
  "name": "fcc387c9-0f16-4071-99e9-969114efb174",
  "origin_id": "ffb4af9b-99d5-4a19-beb1-5c83dbd758c8",
  "parent_id": "e0fc49bb-b19c-4c8f-a519-75bc803e4614",
  "perf_shared_size": "0",
  "perf_unique_size": "0",
  "replica_num": 2,
  "shared_size": "5870977024",
  "size": "21474836480",
  "stripe_num": 4,
  "stripe_size": 262144,
  "thin_provision": true,
  "throttling": {
    "bps": "0",
    "bps_max": "0",
    "bps_max_length": "1",
    "iops": "0",
    "iops_max": "0",
    "iops_max_length": "1"
  },
  "unique_size": "0",
  "vextent_id": []
}

[root@node-128-75 16:53:01 fanyang]$ ./zbs-dev-tools meta volume show --meta-addr 10.0.18.216:10100 --volume-id ffb4af9b-99d5-4a19-beb1-5c83dbd758c8 | ./zbs-dev-tools json query --query 'volume'
{
  "access_points": [],
  "bps_burst": "0",
  "clone_count": "4",
  "created_time": {
    "nseconds": "965386222",
    "seconds": "1712564321"
  },
  "description": "Auto created by backup-st",
  "downgraded_prioritized_space": "0",
  "id": "ffb4af9b-99d5-4a19-beb1-5c83dbd758c8",
  "iops_burst": "0",
  "is_snapshot": true,
  "logical_used_size": "2935488512",
  "name": "fce3eed9-9c9d-43d6-97fd-4d033b8a4f22",
  "nfs_meta": {},
  "origin_id": "a68089cb-f56e-45ae-aaa5-4823d79fa3ee",
  "parent_id": "fcc387c9-0f16-4071-99e9-969114efb174",
  "perf_shared_size": "0",
  "perf_unique_size": "0",
  "prioritized": false,
  "replica_num": 2,
  "shared_size": "5870977024",
  "size": "21474836480",
  "snapshot_pool_id": "e0fc49bb-b19c-4c8f-a519-75bc803e4614",
  "stripe_num": 4,
  "stripe_size": 262144,
  "thin_provision": true,
  "throttling": {
    "bps": "0",
    "bps_max": "0",
    "bps_max_length": "1",
    "iops": "0",
    "iops_max": "0",
    "iops_max_length": "1"
  },
  "unique_size": "0",
  "vextent_id": []
}

[root@node-128-75 16:53:31 fanyang]$ ./zbs-dev-tools meta volume show --meta-addr 10.0.18.216:10100 --volume-id a68089cb-f56e-45ae-aaa5-4823d79fa3ee | ./zbs-dev-tools json query --query 'volume'
{
  "access_points": [],
  "bps_burst": "0",
  "clone_count": "4",
  "created_time": {
    "nseconds": "563945583",
    "seconds": "1712563580"
  },
  "description": "Auto created by backup-st",
  "downgraded_prioritized_space": "0",
  "id": "a68089cb-f56e-45ae-aaa5-4823d79fa3ee",
  "iops_burst": "0",
  "is_snapshot": true,
  "logical_used_size": "2935488512",
  "name": "b0c78f4b-9a64-425f-91ae-c225af4bbab1",
  "nfs_meta": {},
  "origin_id": "5ccb660e-a26b-4a5d-9fba-b9241fcf6469",
  "parent_id": "fcc387c9-0f16-4071-99e9-969114efb174",
  "perf_shared_size": "0",
  "perf_unique_size": "0",
  "prioritized": false,
  "replica_num": 2,
  "shared_size": "5870977024",
  "size": "21474836480",
  "snapshot_pool_id": "e0fc49bb-b19c-4c8f-a519-75bc803e4614",
  "stripe_num": 4,
  "stripe_size": 262144,
  "thin_provision": true,
  "throttling": {
    "bps": "0",
    "bps_max": "0",
    "bps_max_length": "1",
    "iops": "0",
    "iops_max": "0",
    "iops_max_length": "1"
  },
  "unique_size": "0",
  "vextent_id": []
}

[root@node-128-75 16:58:16 fanyang]$ ./zbs-dev-tools meta volume show --meta-addr 10.0.18.216:10100 --volume-id 5ccb660e-a26b-4a5d-9fba-b9241fcf6469 | ./zbs-dev-tools json query --query 'volume'
{
  "access_points": [],
  "bps_burst": "0",
  "clone_count": "4",
  "created_time": {
    "nseconds": "293562337",
    "seconds": "1712563372"
  },
  "description": "Auto created by backup-st",
  "downgraded_prioritized_space": "0",
  "id": "5ccb660e-a26b-4a5d-9fba-b9241fcf6469",
  "iops_burst": "0",
  "is_snapshot": true,
  "logical_used_size": "2935488512",
  "name": "89328081-df9b-4bb1-8808-3253fce040f5",
  "nfs_meta": {},
  "origin_id": "0e64bb0f-5a37-41cc-8efb-099554e54925",
  "parent_id": "fcc387c9-0f16-4071-99e9-969114efb174",
  "perf_shared_size": "0",
  "perf_unique_size": "0",
  "prioritized": false,
  "replica_num": 2,
  "shared_size": "5870977024",
  "size": "21474836480",
  "snapshot_pool_id": "e0fc49bb-b19c-4c8f-a519-75bc803e4614",
  "stripe_num": 4,
  "stripe_size": 262144,
  "thin_provision": true,
  "throttling": {
    "bps": "0",
    "bps_max": "0",
    "bps_max_length": "1",
    "iops": "0",
    "iops_max": "0",
    "iops_max_length": "1"
  },
  "unique_size": "0",
  "vextent_id": []
}
```

```
[root@node-128-75 18:17:53 ~]$ zbs-tool -f json elf show_vol_by_vm_id 1c3ed404-4c3a-4418-97cb-417feb0f3d2c
[
    {
        "Elf Volume": "8323a80b-03e7-4226-b507-bf9861a9c3f3",
        "Volume ID": "a7517fa9-a7b3-4681-80d9-bac89d4bfa00",
        "Name": "a7517fa9-a7b3-4681-80d9-bac89d4bfa00",
        "Parent ID": "e0fc49bb-b19c-4c8f-a519-75bc803e4614",
        "Size": "20.00 GiB",
        "Perf Unique Size": "2.50 MiB",
        "Perf Shared Size": "0.00 B",
        "Cap Unique Size": "3.26 GiB",
        "Cap Shared Size": "0.00 B",
        "Logical Used Size": "1.63 GiB",
        "Creation Time": "2024-04-12 17:33:01.630683566",
        "Status": "VOLUME_ONLINE",
        "Replica Number": 2,
        "Thin Provision": "True",
        "Read Only": "False",
        "Description": "",
        "Alloc Even": "False",
        "Clone count": 0,
        "Prefer CID": 0
    }
]
```

```
[root@node-128-75 18:18:16 ~]$ zbs-meta volume show_by_id a7517fa9-a7b3-4681-80d9-bac89d4bfa00
-----------------  ------------------------------------
ID                 a7517fa9-a7b3-4681-80d9-bac89d4bfa00
Name               a7517fa9-a7b3-4681-80d9-bac89d4bfa00
Parent ID          e0fc49bb-b19c-4c8f-a519-75bc803e4614
Size               20.00 GiB
Perf Unique Size   2.50 MiB
Perf Shared Size   0.00 B
Cap Unique Size    3.26 GiB
Cap Shared Size    0.00 B
Logical Used Size  1.63 GiB
Creation Time      2024-04-12 17:33:01.630683566
Status             VOLUME_ONLINE
Resiliency Type    RT_REPLICA
Replica Num        2
EC Param
Thin Provision     True
Read Only          False
Description
Alloc Even         False
Clone count        0
Iops               0
Iops Read          0
Iops Write         0
Bps                0
Bps Read           0
Bps Write          0
Stripe Num         4
Stripe Size        262144
Prefer CID         0
Access Points      []
Is Prioritized     False
Downgraded PRS     0.00 B
-----------------  ------------------------------------


```

```bash
[root@node-128-75 18:41:44 fanyang]$ zbs-tool -f json elf show_vol_by_vm_id b6f843a1-0e59-42d6-9699-e79cdf498e28
[
    {
        "Elf Volume": "8c65e352-15b5-42cc-bc32-cba5a2b3bb0a",
        "Volume ID": "e7bfda99-df7d-425b-b19e-156bb702f20d",
        "Name": "e7bfda99-df7d-425b-b19e-156bb702f20d",
        "Parent ID": "a1a4227a-7942-47a4-98c3-78163c2c0a37",
        "Size": "20.00 GiB",
        "Perf Unique Size": "0.00 B",
        "Perf Shared Size": "0.00 B",
        "Cap Unique Size": "0.00 B",
        "Cap Shared Size": "0.00 B",
        "Logical Used Size": "0.00 B",
        "Creation Time": "2024-04-12 18:40:23.614927621",
        "Status": "VOLUME_ONLINE",
        "Replica Number": 2,
        "Thin Provision": "True",
        "Read Only": "False",
        "Description": "",
        "Alloc Even": "False",
        "Clone count": 0,
        "Prefer CID": 0
    }
]

[root@node-128-75 18:41:51 fanyang]$ zbs-tool -f json elf show_vol_by_vm_id 8284654a-ca8b-4a92-a9cc-4e3040e34c1d
[
    {
        "Elf Volume": "f4cdaa95-a1fa-48a5-8407-139217f8ebf0",
        "Volume ID": "9056cc55-7d38-4724-af7c-fb13306ed1a2",
        "Name": "9056cc55-7d38-4724-af7c-fb13306ed1a2",
        "Parent ID": "a1a4227a-7942-47a4-98c3-78163c2c0a37",
        "Size": "20.00 GiB",
        "Perf Unique Size": "0.00 B",
        "Perf Shared Size": "0.00 B",
        "Cap Unique Size": "0.00 B",
        "Cap Shared Size": "0.00 B",
        "Logical Used Size": "0.00 B",
        "Creation Time": "2024-04-12 18:40:35.851887477",
        "Status": "VOLUME_ONLINE",
        "Replica Number": 2,
        "Thin Provision": "True",
        "Read Only": "False",
        "Description": "",
        "Alloc Even": "False",
        "Clone count": 0,
        "Prefer CID": 0
    }
]

[root@node-128-75 18:42:16 fanyang]$ zbs-tool -f json elf show_vol_by_vm_id 67f7a5a0-21bc-4322-a7d9-d6473e03b42d
[
    {
        "Elf Volume": "d5eecf0c-04dc-4428-b9ed-64958de0c6cd",
        "Volume ID": "59c0522c-a89b-4d78-96b6-bd5e794b3360",
        "Name": "59c0522c-a89b-4d78-96b6-bd5e794b3360",
        "Parent ID": "a1a4227a-7942-47a4-98c3-78163c2c0a37",
        "Size": "20.00 GiB",
        "Perf Unique Size": "0.00 B",
        "Perf Shared Size": "0.00 B",
        "Cap Unique Size": "0.00 B",
        "Cap Shared Size": "0.00 B",
        "Logical Used Size": "0.00 B",
        "Creation Time": "2024-04-12 18:40:49.275499109",
        "Status": "VOLUME_ONLINE",
        "Replica Number": 2,
        "Thin Provision": "True",
        "Read Only": "False",
        "Description": "",
        "Alloc Even": "False",
        "Clone count": 0,
        "Prefer CID": 0
    }
]

[root@node-128-75 18:42:24 fanyang]$ zbs-tool -f json elf show_vol_by_vm_id 0e541dd5-f8ea-4060-b10a-de455945f76f
[
    {
        "Elf Volume": "67f98187-2aab-4d4a-bb67-aaca0673ce0a",
        "Volume ID": "d9b837e0-96b4-4787-8551-a1f07545e37b",
        "Name": "d9b837e0-96b4-4787-8551-a1f07545e37b",
        "Parent ID": "a1a4227a-7942-47a4-98c3-78163c2c0a37",
        "Size": "20.00 GiB",
        "Perf Unique Size": "In Process",
        "Perf Shared Size": "In Process",
        "Cap Unique Size": "In Process",
        "Cap Shared Size": "In Process",
        "Logical Used Size": "In Process",
        "Creation Time": "2024-04-12 18:41:08.851552701",
        "Status": "VOLUME_ONLINE",
        "Replica Number": 2,
        "Thin Provision": "True",
        "Read Only": "False",
        "Description": "",
        "Alloc Even": "False",
        "Clone count": 0,
        "Prefer CID": 0
    }
]

[
    {
        "Elf Volume": "32d2077d-9530-432c-aeb2-d8e54fc3252e",
        "Volume ID": "fcc387c9-0f16-4071-99e9-969114efb174",
        "Name": "fcc387c9-0f16-4071-99e9-969114efb174",
        "Parent ID": "e0fc49bb-b19c-4c8f-a519-75bc803e4614",
        "Size": "20.00 GiB",
        "Perf Unique Size": "0.00 B",
        "Perf Shared Size": "0.00 B",
        "Cap Unique Size": "0.00 B",
        "Cap Shared Size": "5.47 GiB",
        "Logical Used Size": "2.73 GiB",
        "Creation Time": "2024-02-04 19:43:40.72484941",
        "Status": "VOLUME_ONLINE",
        "Replica Number": 2,
        "Thin Provision": "True",
        "Read Only": "False",
        "Description": "",
        "Alloc Even": "False",
        "Clone count": 4,
        "Prefer CID": 0
    }
]
```
