```bash
root@yl-readtest:/mnt/data1# timedatectl set-ntp false
root@yl-readtest:/mnt/data1# timedatectl
               Local time: Tue 2025-08-19 16:03:26 CST
           Universal time: Tue 2025-08-19 08:03:26 UTC
                 RTC time: Tue 2025-08-19 08:03:26
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: yes
              NTP service: inactive
          RTC in local TZ: no
root@yl-readtest:/mnt/data1# timedatectl set-time "2025-08-19 20:03:26"
root@yl-readtest:/mnt/data1# timedatectl
               Local time: Tue 2025-08-19 20:03:33 CST
           Universal time: Tue 2025-08-19 12:03:33 UTC
                 RTC time: Tue 2025-08-19 12:03:33
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: no
              NTP service: inactive
          RTC in local TZ: no

root@yl-readtest:/mnt/data1# sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw prepare

root@yl-readtest:/mnt/data1# stat test_file.127
  File: test_file.127
  Size: 16777216  	Blocks: 32768      IO Block: 4096   regular file
Device: 8,1	Inode: 33666       Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:unlabeled_t:s0
Access: 2025-08-19 20:03:57.407745141 +0800
Modify: 2025-08-19 20:03:57.413745649 +0800
Change: 2025-08-19 20:03:57.413745649 +0800
 Birth: 2025-08-19 20:03:57.407745141 +0800

root@yl-readtest:/mnt/data1# timedatectl set-time "2025-08-19 16:03:26"
root@yl-readtest:/mnt/data1# timedatectl
               Local time: Tue 2025-08-19 16:03:28 CST
           Universal time: Tue 2025-08-19 08:03:28 UTC
                 RTC time: Tue 2025-08-19 08:03:29
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: no
              NTP service: inactive
          RTC in local TZ: no

root@yl-readtest:/mnt/data1# sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw run
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
    reads/s:                      22439.49
    writes/s:                     14960.33
    fsyncs/s:                     48117.77

Throughput:
    read, MiB/s:                  350.62
    written, MiB/s:               233.76

General statistics:
    total time:                          10.0065s
    total number of events:              853306

Latency (ms):
         min:                                    0.00
         avg:                                    0.23
         max:                                   18.32
         95th percentile:                        1.04
         sum:                               197252.82

Threads fairness:
    events (avg/stddev):           42665.3000/811.18
    execution time (avg/stddev):   9.8626/0.00

root@yl-readtest:/mnt/data1# stat test_file.127
  File: test_file.127
  Size: 16777216  	Blocks: 32768      IO Block: 4096   regular file
Device: 8,1	Inode: 33666       Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:unlabeled_t:s0
Access: 2025-08-19 16:04:08.309660160 +0800
Modify: 2025-08-19 16:04:08.303659652 +0800
Change: 2025-08-19 16:04:08.303659652 +0800
 Birth: 2025-08-19 20:03:57.407745141 +0800

```
