```shell
pgbench -h 127.0.0.1 -U pgdog -p 6432 pgdog -t 1000 -c 10 --protocol simple -P 1
pgbench -h 127.0.0.1 -U pgdog -p 6432 pgdog -t 1000 -c 10 --protocol extended -P 1
pgbench -h 127.0.0.1 -U pgdog -p 6432 pgdog -t 1000 -c 10 --protocol prepared -P 1
```

```shell
pgbench -t 1000 -c 10 --protocol simple -P 1
```

## WAL 跟 Data 一个卷

- xfs

### write through

```bash
pgbench -t 1000 -c 10 --protocol simple -P 1
pgbench -t 1000 -c 10 --protocol extended -P 1
pgbench -t 1000 -c 10 --protocol prepared -P 1
```

```bash
bash-5.1$ pgbench -t 1000 -c 10 --protocol simple -P 1
starting vacuum...end.
progress: 1.0 s, 1428.9 tps, lat 6.710 ms stddev 5.853
progress: 2.0 s, 1428.0 tps, lat 6.991 ms stddev 7.723
progress: 3.0 s, 1310.0 tps, lat 7.561 ms stddev 10.191
progress: 4.0 s, 1319.0 tps, lat 7.650 ms stddev 10.472
progress: 5.0 s, 1216.0 tps, lat 8.184 ms stddev 11.860
progress: 6.0 s, 1591.1 tps, lat 6.295 ms stddev 8.423
progress: 7.0 s, 1444.0 tps, lat 6.844 ms stddev 10.026
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 10
number of threads: 1
number of transactions per client: 1000
number of transactions actually processed: 10000/10000
latency average = 7.069 ms
latency stddev = 9.314 ms
tps = 1388.000899 (including connections establishing)
tps = 1388.668868 (excluding connections establishing)
```

```bash
bash-5.1$ pgbench -t 1000 -c 10 --protocol extended -P 1
starting vacuum...end.
progress: 1.0 s, 1098.0 tps, lat 8.661 ms stddev 11.550
progress: 2.0 s, 1192.0 tps, lat 8.323 ms stddev 11.539
progress: 3.0 s, 1402.1 tps, lat 7.226 ms stddev 9.352
progress: 4.0 s, 1205.9 tps, lat 8.049 ms stddev 11.782
progress: 5.0 s, 1350.9 tps, lat 7.575 ms stddev 9.759
progress: 6.0 s, 1234.1 tps, lat 7.951 ms stddev 10.773
progress: 7.0 s, 1189.1 tps, lat 8.576 ms stddev 12.254
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: extended
number of clients: 10
number of threads: 1
number of transactions per client: 1000
number of transactions actually processed: 10000/10000
latency average = 7.710 ms
latency stddev = 10.530 ms
tps = 1275.585980 (including connections establishing)
tps = 1276.151371 (excluding connections establishing)
```

```bash
bash-5.1$ pgbench -t 1000 -c 10 --protocol prepared -P 1
starting vacuum...end.
progress: 1.0 s, 1535.0 tps, lat 6.239 ms stddev 8.528
progress: 2.0 s, 1557.9 tps, lat 6.420 ms stddev 10.448
progress: 3.0 s, 1483.0 tps, lat 6.730 ms stddev 10.515
progress: 4.0 s, 1400.9 tps, lat 6.892 ms stddev 10.298
progress: 5.0 s, 1599.1 tps, lat 6.432 ms stddev 9.415
progress: 6.0 s, 1797.0 tps, lat 5.579 ms stddev 7.977
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: prepared
number of clients: 10
number of threads: 1
number of transactions per client: 1000
number of transactions actually processed: 10000/10000
latency average = 6.265 ms
latency stddev = 9.483 ms
tps = 1569.634727 (including connections establishing)
tps = 1570.429070 (excluding connections establishing)
```

```
-M, --protocol=simple|extended|prepared
                         protocol for submitting queries (default: simple)
```

## 无法设置 write back

```bash
$ echo "write back" | tee /sys/block/sdb/queue/write_cache
write back
tee: /sys/block/sdb/queue/write_cache: Invalid argument
```

```
[root@fanyang-pgbench ~]# lsscsi
[0:0:0:0]    disk    INTEL    SMARTX ZBS       0001  /dev/sda
[0:0:1:0]    disk    INTEL    SMARTX ZBS       0001  /dev/sdb
[1:0:0:0]    cd/dvd  QEMU     QEMU DVD-ROM     2.5+  /dev/sr0
```

### WAL 分离后

```
[root@fanyang-pgbench ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0   40G  0 disk
└─sda1   8:1    0   40G  0 part /var/lib/pgsql/data/pg_wal
sdb      8:16   0  256G  0 disk
└─sdb1   8:17   0  256G  0 part /var/lib/pgsql/data
sr0     11:0    1   11G  0 rom  /mnt/iso
vda    253:0    0  128G  0 disk
├─vda1 253:1    0    1G  0 part /boot
├─vda2 253:2    0  7.8G  0 part [SWAP]
├─vda3 253:3    0   70G  0 part /
├─vda4 253:4    0    1K  0 part
└─vda5 253:5    0 49.2G  0 part /home
```

```
bash-5.1$ pgbench -t 1000 -c 10 -P 1
starting vacuum...end.
progress: 1.0 s, 1284.0 tps, lat 7.401 ms stddev 6.469
progress: 2.0 s, 1315.9 tps, lat 7.530 ms stddev 8.052
progress: 3.0 s, 1281.0 tps, lat 7.576 ms stddev 7.897
progress: 4.0 s, 1323.0 tps, lat 7.590 ms stddev 8.853
progress: 5.0 s, 1144.8 tps, lat 8.907 ms stddev 11.737
progress: 6.0 s, 1333.2 tps, lat 7.451 ms stddev 9.085
progress: 7.0 s, 1518.0 tps, lat 5.610 ms stddev 5.389
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 10
number of threads: 1
number of transactions per client: 1000
number of transactions actually processed: 10000/10000
latency average = 7.057 ms
latency stddev = 8.226 ms
tps = 1320.024715 (including connections establishing)
tps = 1320.738278 (excluding connections establishing)
```

## 改进测试方法

```
bash-5.1$ pgbench -i -s 10000
```

```
bash-5.1$ pgbench -T 20 -c 50 -P 1
starting vacuum...end.
progress: 1.0 s, 5585.6 tps, lat 7.144 ms stddev 4.060
progress: 2.0 s, 6374.9 tps, lat 7.422 ms stddev 6.503
progress: 3.0 s, 7335.0 tps, lat 6.795 ms stddev 5.052
progress: 4.0 s, 7366.3 tps, lat 6.529 ms stddev 2.646
progress: 5.0 s, 7807.0 tps, lat 6.090 ms stddev 2.471
progress: 6.0 s, 8250.6 tps, lat 5.894 ms stddev 2.599
progress: 7.0 s, 7850.8 tps, lat 6.099 ms stddev 2.639
progress: 8.0 s, 7293.5 tps, lat 6.615 ms stddev 3.790
progress: 9.0 s, 6656.4 tps, lat 7.329 ms stddev 7.105
progress: 10.0 s, 7243.8 tps, lat 6.732 ms stddev 6.532
progress: 11.0 s, 8147.2 tps, lat 5.900 ms stddev 2.440
progress: 12.0 s, 8240.0 tps, lat 5.830 ms stddev 2.244
progress: 13.0 s, 8114.9 tps, lat 5.907 ms stddev 2.350
progress: 14.0 s, 8207.9 tps, lat 5.858 ms stddev 2.267
progress: 15.0 s, 7891.7 tps, lat 6.147 ms stddev 3.604
progress: 16.0 s, 6844.0 tps, lat 7.056 ms stddev 6.823
progress: 17.0 s, 6995.0 tps, lat 6.995 ms stddev 5.924
progress: 18.0 s, 7935.2 tps, lat 6.040 ms stddev 2.457
progress: 19.0 s, 8310.3 tps, lat 5.805 ms stddev 2.439
progress: 20.0 s, 7622.5 tps, lat 6.304 ms stddev 2.741
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1000
query mode: simple
number of clients: 50
number of threads: 1
duration: 20 s
number of transactions actually processed: 150123
latency average = 6.378 ms
latency stddev = 4.120 ms
tps = 7497.778850 (including connections establishing)
tps = 7499.023095 (excluding connections establishing)
```

## Optane 900P

```bash
initdb -D ./data1
postgres -D ./data1 -c max_wal_size=30GB -c checkpoint_completion_target=0.9 -c unix_socket_directories=.
```

```bash
-> % pgbench -i -s 1000 -h /mnt/sysbench/data1 postgres
```

generating data (client-side)...

![[Pasted image 20250821190103.png]]

creating primary keys...

![[Pasted image 20250821190120.png]]

```
-> % pgbench -T 20 -c 100 -P 1 -j 10 -h /mnt/sysbench/data1 postgres
pgbench (17.5)
starting vacuum...end.
progress: 1.0 s, 52461.1 tps, lat 1.752 ms stddev 0.600, 0 failed
progress: 2.0 s, 68604.2 tps, lat 1.447 ms stddev 0.440, 0 failed
progress: 3.0 s, 71098.4 tps, lat 1.396 ms stddev 0.401, 0 failed
progress: 4.0 s, 73431.1 tps, lat 1.351 ms stddev 0.410, 0 failed
progress: 5.0 s, 76367.0 tps, lat 1.302 ms stddev 0.369, 0 failed
progress: 6.0 s, 80881.6 tps, lat 1.228 ms stddev 0.314, 0 failed
progress: 7.0 s, 84274.1 tps, lat 1.179 ms stddev 0.281, 0 failed
progress: 8.0 s, 85445.6 tps, lat 1.162 ms stddev 0.276, 0 failed
progress: 9.0 s, 86314.9 tps, lat 1.151 ms stddev 0.270, 0 failed
progress: 10.0 s, 86851.7 tps, lat 1.143 ms stddev 0.302, 0 failed
progress: 11.0 s, 88361.9 tps, lat 1.124 ms stddev 0.236, 0 failed
progress: 12.0 s, 89056.7 tps, lat 1.115 ms stddev 0.232, 0 failed
progress: 13.0 s, 89599.3 tps, lat 1.108 ms stddev 0.251, 0 failed
progress: 14.0 s, 88963.0 tps, lat 1.115 ms stddev 0.256, 0 failed
progress: 15.0 s, 90256.5 tps, lat 1.100 ms stddev 0.240, 0 failed
progress: 16.0 s, 90613.4 tps, lat 1.095 ms stddev 0.233, 0 failed
progress: 17.0 s, 91095.8 tps, lat 1.089 ms stddev 0.243, 0 failed
progress: 18.0 s, 91898.2 tps, lat 1.079 ms stddev 0.224, 0 failed
progress: 19.0 s, 91855.1 tps, lat 1.080 ms stddev 0.258, 0 failed
progress: 20.0 s, 24259.6 tps, lat 4.109 ms stddev 17.925, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1000
query mode: simple
number of clients: 100
number of threads: 10
maximum number of tries: 1
duration: 20 s
number of transactions actually processed: 1601719
number of failed transactions: 0 (0.000%)
latency average = 1.235 ms
latency stddev = 2.260 ms
initial connection time = 73.335 ms
tps = 80354.341536 (without initial connection time)
```

![[Pasted image 20250821190559.png]]

```bash
# 不分离
[fanmi@fanyang-pgbench ~]$ pgbench -T 20 -c 100 -P 1 -j 10 -h /mnt/pg-data/data1 postgres
starting vacuum...end.
progress: 1.0 s, 9500.4 tps, lat 9.853 ms stddev 9.367
progress: 2.0 s, 10340.4 tps, lat 9.610 ms stddev 9.580
progress: 3.0 s, 11660.5 tps, lat 8.469 ms stddev 8.701
progress: 4.0 s, 11378.1 tps, lat 8.727 ms stddev 9.409
progress: 5.0 s, 10710.7 tps, lat 9.243 ms stddev 9.271
progress: 6.0 s, 12162.7 tps, lat 8.158 ms stddev 8.454
progress: 7.0 s, 12561.1 tps, lat 7.886 ms stddev 9.019
progress: 8.0 s, 12481.9 tps, lat 7.930 ms stddev 8.363
progress: 9.0 s, 12765.2 tps, lat 7.734 ms stddev 8.761
progress: 10.0 s, 13129.8 tps, lat 7.541 ms stddev 8.026
progress: 11.0 s, 13760.0 tps, lat 7.183 ms stddev 8.010
progress: 12.0 s, 13642.1 tps, lat 7.154 ms stddev 8.051
progress: 13.0 s, 12457.0 tps, lat 7.995 ms stddev 8.723
progress: 14.0 s, 14197.6 tps, lat 6.986 ms stddev 8.367
progress: 15.0 s, 14334.4 tps, lat 6.762 ms stddev 8.369
progress: 16.0 s, 14846.2 tps, lat 6.750 ms stddev 8.373
progress: 17.0 s, 14871.8 tps, lat 6.548 ms stddev 7.568
progress: 18.0 s, 12288.0 tps, lat 7.939 ms stddev 8.870
progress: 19.0 s, 13231.2 tps, lat 7.693 ms stddev 9.068
progress: 20.0 s, 14744.4 tps, lat 6.735 ms stddev 7.631
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1000
query mode: simple
number of clients: 100
number of threads: 10
duration: 20 s
number of transactions actually processed: 255166
latency average = 7.738 ms
latency stddev = 8.610 ms
tps = 12741.412877 (including connections establishing)
tps = 12744.798847 (excluding connections establishing)
```

![[Pasted image 20250821194928.png]]

![[Pasted image 20250821194947.png]]

```
[fanmi@fanyang-pgbench ~]$ pgbench -i -s 1000 -h /mnt/pg-data/data2 postgres
dropping old tables...
NOTICE:  table "pgbench_accounts" does not exist, skipping
NOTICE:  table "pgbench_branches" does not exist, skipping
NOTICE:  table "pgbench_history" does not exist, skipping
NOTICE:  table "pgbench_tellers" does not exist, skipping
creating tables...
generating data (client-side)...
100000000 of 100000000 tuples (100%) done (elapsed 176.09 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 275.23 s (drop tables 0.00 s, create tables 0.02 s, client-side generate 176.76 s, vacuum 25.23 s, primary keys 73.22 s).
```

```bash
# 分离 wal 和 data，两个卷
[fanmi@fanyang-pgbench ~]$ pgbench -T 20 -c 100 -P 1 -j 10 -h /mnt/pg-data/data2 postgres
starting vacuum...end.
progress: 1.0 s, 10889.3 tps, lat 8.530 ms stddev 8.613
progress: 2.0 s, 12562.7 tps, lat 7.764 ms stddev 8.510
progress: 3.0 s, 12929.2 tps, lat 7.684 ms stddev 8.717
progress: 4.0 s, 13184.0 tps, lat 7.552 ms stddev 8.555
progress: 5.0 s, 13027.8 tps, lat 7.658 ms stddev 8.425
progress: 6.0 s, 14192.2 tps, lat 6.938 ms stddev 8.252
progress: 7.0 s, 14031.9 tps, lat 6.862 ms stddev 8.028
progress: 8.0 s, 14420.9 tps, lat 6.773 ms stddev 7.839
progress: 9.0 s, 14913.0 tps, lat 6.873 ms stddev 8.703
progress: 10.0 s, 14858.0 tps, lat 6.568 ms stddev 7.771
progress: 11.0 s, 14676.2 tps, lat 6.794 ms stddev 7.748
progress: 12.0 s, 14662.0 tps, lat 6.563 ms stddev 7.894
progress: 13.0 s, 15722.2 tps, lat 6.448 ms stddev 8.060
progress: 14.0 s, 15542.8 tps, lat 6.354 ms stddev 7.615
progress: 15.0 s, 15950.2 tps, lat 6.143 ms stddev 6.872
progress: 16.0 s, 16098.0 tps, lat 6.160 ms stddev 7.173
progress: 17.0 s, 16428.1 tps, lat 5.986 ms stddev 6.700
progress: 18.0 s, 16454.8 tps, lat 5.979 ms stddev 6.958
progress: 19.0 s, 17249.6 tps, lat 5.696 ms stddev 6.768
progress: 20.0 s, 16304.2 tps, lat 6.029 ms stddev 7.549
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1000
query mode: simple
number of clients: 100
number of threads: 10
duration: 20 s
number of transactions actually processed: 294197
latency average = 6.694 ms
latency stddev = 7.838 ms
tps = 14690.842707 (including connections establishing)
tps = 14694.887938 (excluding connections establishing)
```
