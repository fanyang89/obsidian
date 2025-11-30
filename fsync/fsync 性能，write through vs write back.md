```
fanmi@fanyang-epyc [00:09:49] [~]
-> % echo "write through" | sudo tee /sys/block/nvme9n1/queue/write_cache
[sudo] password for fanmi:
write through

fanmi@fanyang-epyc [00:09:57] [~]
-> % cat /sys/block/nvme9n1/queue/write_cache
write through

fanmi@fanyang-epyc [00:09:59] [~]
-> % sudo umount /mnt/sysbench

fanmi@fanyang-epyc [00:10:20] [~]
-> % sudo mount -o defaults,noatime /dev/nvme9n1p1 /mnt/sysbench

fanmi@fanyang-epyc [00:10:20] [~]
-> % cd /mnt/sysbench

fanmi@fanyang-epyc [00:10:25] [/mnt/sysbench]
-> % pg_test_fsync
5 seconds per test
O_DIRECT supported on this platform for open_datasync and open_sync.

Compare file sync methods using one 8kB write:
(in "wal_sync_method" preference order, except fdatasync is Linux's default)
        open_datasync                     53951.698 ops/sec      19 usecs/op
        fdatasync                         48874.356 ops/sec      20 usecs/op
        fsync                             44107.947 ops/sec      23 usecs/op
        fsync_writethrough                              n/a
        open_sync                         48927.873 ops/sec      20 usecs/op

Compare file sync methods using two 8kB writes:
(in "wal_sync_method" preference order, except fdatasync is Linux's default)
        open_datasync                     27363.518 ops/sec      37 usecs/op
        fdatasync                         37382.131 ops/sec      27 usecs/op
        fsync                             18987.327 ops/sec      53 usecs/op
        fsync_writethrough                              n/a
        open_sync                          3367.083 ops/sec     297 usecs/op

Compare open_sync with different write sizes:
(This is designed to compare the cost of writing 16kB in different write
open_sync sizes.)
         1 * 16kB open_sync write          3383.669 ops/sec     296 usecs/op
         2 *  8kB open_sync writes         3375.601 ops/sec     296 usecs/op
         4 *  4kB open_sync writes         3383.060 ops/sec     296 usecs/op
         8 *  2kB open_sync writes         1105.033 ops/sec     905 usecs/op
        16 *  1kB open_sync writes          597.940 ops/sec    1672 usecs/op

Test if fsync on non-write file descriptor is honored:
(If the times are similar, fsync() can sync data written on a different
descriptor.)
        write, fsync, close                3408.703 ops/sec     293 usecs/op
        write, close, fsync                3421.788 ops/sec     292 usecs/op

Non-sync'ed 8kB writes:
        write                            667359.132 ops/sec       1 usecs/op

fanmi@fanyang-epyc [00:11:45] [/mnt/sysbench]
-> % python
Python 3.13.5 (main, Jun 21 2025, 09:35:00) [GCC 15.1.1 20250425] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 44107 / 761
57.959264126149804
>>>

fanmi@fanyang-epyc [00:12:05] [/mnt/sysbench]
-> % sudo mount -o defaults,noatime /dev/nvme9n1p1 /mnt/sysbench

fanmi@fanyang-epyc [00:12:08] [/mnt/sysbench]
-> % python c

fanmi@fanyang-epyc [00:12:13] [/mnt/sysbench]
-> %

fanmi@fanyang-epyc [00:12:14] [/mnt/sysbench]
-> % cd

fanmi@fanyang-epyc [00:12:17] [~]
-> % sudo umount /mnt/sysbench

fanmi@fanyang-epyc [00:12:22] [~]
-> % echo "write back" | sudo tee /sys/block/nvme9n1/queue/write_cache
write back

fanmi@fanyang-epyc [00:12:27] [~]
-> % cat /sys/block/nvme9n1/queue/write_cache
write back

fanmi@fanyang-epyc [00:12:34] [~]
-> % sudo mount -o defaults,noatime /dev/nvme9n1p1 /mnt/sysbench

fanmi@fanyang-epyc [00:12:34] [~]
-> % cd /mnt/sysbench

fanmi@fanyang-epyc [00:12:40] [/mnt/sysbench]
-> % pg_test_fsync
5 seconds per test
O_DIRECT supported on this platform for open_datasync and open_sync.

Compare file sync methods using one 8kB write:
(in "wal_sync_method" preference order, except fdatasync is Linux's default)
        open_datasync                      1361.870 ops/sec     734 usecs/op
        fdatasync                          2049.524 ops/sec     488 usecs/op
        fsync                               789.992 ops/sec    1266 usecs/op
        fsync_writethrough                              n/a
        open_sync                           790.394 ops/sec    1265 usecs/op

Compare file sync methods using two 8kB writes:
(in "wal_sync_method" preference order, except fdatasync is Linux's default)
        open_datasync                       681.784 ops/sec    1467 usecs/op
        fdatasync                          2060.592 ops/sec     485 usecs/op
        fsync                               764.837 ops/sec    1307 usecs/op
        fsync_writethrough                              n/a
        open_sync                           377.606 ops/sec    2648 usecs/op

Compare open_sync with different write sizes:
(This is designed to compare the cost of writing 16kB in different write
open_sync sizes.)
         1 * 16kB open_sync write           755.127 ops/sec    1324 usecs/op
         2 *  8kB open_sync writes          379.263 ops/sec    2637 usecs/op
         4 *  4kB open_sync writes          188.890 ops/sec    5294 usecs/op
         8 *  2kB open_sync writes           95.090 ops/sec   10516 usecs/op
        16 *  1kB open_sync writes           48.431 ops/sec   20648 usecs/op

Test if fsync on non-write file descriptor is honored:
(If the times are similar, fsync() can sync data written on a different
descriptor.)
        write, fsync, close                 764.329 ops/sec    1308 usecs/op
        write, close, fsync                 769.495 ops/sec    1300 usecs/op

Non-sync'ed 8kB writes:
        write                            658833.078 ops/sec       2 usecs/op
```