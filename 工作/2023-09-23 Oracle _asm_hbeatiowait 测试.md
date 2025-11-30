```
[grid@rac5 ~]$ sqlplus / as sysasm

SQL*Plus: Release 12.1.0.1.0 Production on Sat Sep 23 00:19:10 2023

Copyright (c) 1982, 2013, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.1.0 - 64bit Production
With the Real Application Clusters and Automatic Storage Management options

SQL> select ksppinm,ksppstvl,ksppdesc from x$ksppi x,x$ksppcv y where x.indx = y.indx and ksppinm='_asm_hbeatiowait';

KSPPINM
--------------------------------------------------------------------------------
KSPPSTVL
--------------------------------------------------------------------------------
KSPPDESC
--------------------------------------------------------------------------------
_asm_hbeatiowait
15
number of secs to wait for PST Async Hbeat IO return
```

```bash
[root@hygon-node-19-97 15:34:18 ~]$zbs-chunk volume inject_latency --readwrite_latency_ms 16000 ea64a08a-a056-436b-9d45-fbac3d3d34b3
-----------------  ------------------------------------
Volume ID          ea64a08a-a056-436b-9d45-fbac3d3d34b3
Read Latency       0 ms
Write Latency      0 ms
ReadWrite Latency  16000 ms
-----------------  ------------------------------------
[root@hygon-node-19-97 15:34:32 ~]$zbs-chunk volume inject_latency --readwrite_latency_ms 16000 88574e84-abdc-43f7-a4b9-9ffeba4eb382
-----------------  ------------------------------------
Volume ID          88574e84-abdc-43f7-a4b9-9ffeba4eb382
Read Latency       0 ms
Write Latency      0 ms
ReadWrite Latency  16000 ms
-----------------  ------------------------------------
[root@hygon-node-19-97 15:34:41 ~]$zbs-chunk volume inject_latency --readwrite_latency_ms 16000 f2ed9bda-10ea-40a5-b8f3-80cf9255a4df
-----------------  ------------------------------------
Volume ID          f2ed9bda-10ea-40a5-b8f3-80cf9255a4df
Read Latency       0 ms
Write Latency      0 ms
ReadWrite Latency  16000 ms

# rac7
* node 0 does not exist. instance_number = 1
Sat Sep 23 15:34:48 2023
WARNING: Waited 15 secs for write IO to PST disk 2 in group 1.
WARNING: Waited 15 secs for write IO to PST disk 2 in group 1.
Sat Sep 23 15:35:38 2023
WARNING: Waited 15 secs for write IO to PST disk 0 in group 1.
WARNING: Waited 15 secs for write IO to PST disk 1 in group 1.
WARNING: Waited 15 secs for write IO to PST disk 2 in group 1.
Sat Sep 23 15:35:38 2023
NOTE: cache dismounting (not clean) group 1/0x478D8324 (CRS)
NOTE: messaging CKPT to quiesce pins Unix process pid: 63639, image: oracle@rac7 (B000)
Sat Sep 23 15:35:38 2023
NOTE: halting all I/Os to diskgroup 1 (CRS)

# rac8
Sat Sep 23 15:35:53 2023
WARNING: Waited 15 secs for write IO to PST disk 0 in group 1.
WARNING: Waited 15 secs for write IO to PST disk 1 in group 1.
WARNING: Waited 15 secs for write IO to PST disk 2 in group 1.
```

```
NOTE: cache dismounted group 1/0x478D8324 (CRS)
Sat Sep 23 15:35:38 2023
SQL> alter diskgroup CRS dismount force /* ASM SERVER:1200456484 */
Sat Sep 23 15:35:38 2023
NOTE: cache deleting context for group CRS 1/0x478d8324
Sat Sep 23 15:35:38 2023
GMON dismounting group 1 at 19 for pid 33, osid 63639
Sat Sep 23 15:35:38 2023
NOTE: Disk CRS_0000 in mode 0x7f marked for de-assignment
NOTE: Disk CRS_0001 in mode 0x7f marked for de-assignment
NOTE: Disk CRS_0002 in mode 0x7f marked for de-assignment
```

![](assets/Pasted%20image%2020230923154356.png)
SQL> SELECT NAME, STATE, TOTAL_MB, FREE_MB FROM V$ASM_DISKGROUP;

NAME STATE TOTAL_MB FREE_MB

---

FRA MOUNTED 204796 204462
DATA MOUNTED 204796 202820
CRS DISMOUNTED 0 0

```
[root@hygon-node-19-97 15:48:44 ~]$zbs-chunk volume clear_inject --all
[root@hygon-node-19-97 15:48:50 ~]$zbs-chunk volume list_inject
injections was empty
```
