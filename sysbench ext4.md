## fsync

```bash
root@yl-readtest:/mnt/data1# sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw --file-fsync-mode=fsync run
sysbench 1.0.20 (using system LuaJIT 2.1.1713773202)

Running the test with following options:
Number of threads: 20
Initializing random number generator from current time


Extra file open flags: (none)
128 files, 16MiB each
2GiB total file size
Block size 16KiB
Number of IO requests: 0
Read/Write ratio for combined random IO test: 1.50
Periodic FSYNC enabled, calling fsync() each 100 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing random r/w test
Initializing worker threads...

Threads started!


File operations:
    reads/s:                      16457.38
    writes/s:                     10971.75
    fsyncs/s:                     35358.31

Throughput:
    read, MiB/s:                  257.15
    written, MiB/s:               171.43

General statistics:
    total time:                          10.0060s
    total number of events:              625792

Latency (ms):
         min:                                    0.00
         avg:                                    0.32
         max:                                   26.31
         95th percentile:                        2.71
         sum:                               197231.87

Threads fairness:
    events (avg/stddev):           31289.6000/431.72
    execution time (avg/stddev):   9.8616/0.00
```

```bash
root@yl-readtest:/mnt/data1# sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw --file-fsync-mode=fdatasync run
sysbench 1.0.20 (using system LuaJIT 2.1.1713773202)

Running the test with following options:
Number of threads: 20
Initializing random number generator from current time


Extra file open flags: (none)
128 files, 16MiB each
2GiB total file size
Block size 16KiB
Number of IO requests: 0
Read/Write ratio for combined random IO test: 1.50
Periodic FSYNC enabled, calling fsync() each 100 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing random r/w test
Initializing worker threads...

Threads started!


File operations:
    reads/s:                      123260.65
    writes/s:                     82173.64
    fsyncs/s:                     263210.84

Throughput:
    read, MiB/s:                  1925.95
    written, MiB/s:               1283.96

General statistics:
    total time:                          10.0042s
    total number of events:              4686775

Latency (ms):
         min:                                    0.00
         avg:                                    0.04
         max:                                    8.61
         95th percentile:                        0.18
         sum:                               186415.42

Threads fairness:
    events (avg/stddev):           234338.7500/2244.75
    execution time (avg/stddev):   9.3208/0.01
```

```
root@yl-readtest:~# tail ext4-wal-fdatasync.log
Found expected sequence 129, type 2 (commit block) at block 1154
Found expected sequence 130, type 1 (descriptor block) at block 1155
Found expected sequence 130, type 2 (commit block) at block 1163
Found expected sequence 131, type 1 (descriptor block) at block 1164
Found expected sequence 131, type 2 (commit block) at block 1173
Found expected sequence 132, type 1 (descriptor block) at block 1174
Found expected sequence 132, type 2 (commit block) at block 1184
Found expected sequence 133, type 1 (descriptor block) at block 1185
Found expected sequence 133, type 2 (commit block) at block 1195
No magic number at block 1196: end of journal.
root@yl-readtest:~# tail ext4-wal-fsync.log
Found expected sequence 4372, type 2 (commit block) at block 44581
Found expected sequence 4373, type 1 (descriptor block) at block 44582
Found expected sequence 4373, type 2 (commit block) at block 44592
Found expected sequence 4374, type 1 (descriptor block) at block 44593
Found expected sequence 4374, type 2 (commit block) at block 44603
Found expected sequence 4375, type 1 (descriptor block) at block 44604
Found expected sequence 4375, type 2 (commit block) at block 44613
Found expected sequence 4376, type 1 (descriptor block) at block 44614
Found expected sequence 4376, type 2 (commit block) at block 44620
No magic number at block 44621: end of journal.
```

```bash
-> % ssh fedora-yl
root@yl-readtest:~# uname -r
6.11.4-301.fc41.x86_64
```
