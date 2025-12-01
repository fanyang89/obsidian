## 测试环境

```
[root@node-6794 14:50:49 pdisk]$ uname -r
4.19.90-2307.3.0.oe1.v97.x86_64
```

```
$ findmnt .
TARGET     SOURCE         FSTYPE OPTIONS
/mnt/pdisk /dev/nvme3n1p2 xfs    rw,relatime,attr2,inode64,noquota
```

```
[root@node-6794 14:51:47 pdisk]$ lshw -c storage -short
H/W path           Device           Class          Description
==============================================================
/0/100/11.5                         storage        C620 Series Chipset Family SSATA Controller [AHCI mode]
/0/100/14/0/e/4/2                   storage        iDRAC VIRTUAL MEDIA
/0/100/17                           storage        C620 Series Chipset Family SATA Controller [AHCI mode]
/0/101/0           scsi1            storage        SAS3008 PCI-Express Fusion-MPT SAS-3
/0/103/0           scsi16           storage        88SE9230 PCIe 2.0 x2 4-port SATA 6 Gb/s RAID Controller
/0/104/0/4/0                        storage        NVMe DC SSD [3DNAND, Beta Rock Controller]
/0/104/0/5/0                        storage        NVMe Datacenter SSD [3DNAND, Beta Rock Controller]
/0/104/0/6/0                        storage        NVMe Datacenter SSD [Optane]
/0/104/0/7/0                        storage        NVMe Datacenter SSD [3DNAND, Beta Rock Controller]
/0/2/0                              storage        NVMe SSD Controller PM173X
/0/107/0                            storage        NVMe SSD Controller PM173X
/0/f4              scsi0            storage
/0/f5              scsi20           storage
/0/f6              scsi19           storage
```

```
[root@node-6794 14:52:57 pdisk]$ smartctl -H -A /dev/nvme3n1
smartctl 7.1 2019-12-30 r5022 [x86_64-linux-4.19.90-2307.3.0.oe1.v97.x86_64] (local build)
Copyright (C) 2002-19, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        32 Celsius
Available Spare:                    99%
Available Spare Threshold:          10%
Percentage Used:                    13%
Data Units Read:                    2,909,940,646 [1.48 PB]
Data Units Written:                 5,566,791,929 [2.85 PB]
Host Read Commands:                 91,516,329,246
Host Write Commands:                136,066,290,104
Controller Busy Time:               14,048
Power Cycles:                       423
Power On Hours:                     31,715
Unsafe Shutdowns:                   399
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
Warning  Comp. Temperature Time:    78
Critical Comp. Temperature Time:    0
```

```
$ smartctl -i /dev/nvme3n1
smartctl 7.1 2019-12-30 r5022 [x86_64-linux-4.19.90-2307.3.0.oe1.v97.x86_64] (local build)
Copyright (C) 2002-19, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Number:                       SSDPE2KE016T8L
Serial Number:                      PHLN1141003Z1P6AGN
Firmware Version:                   VDV1LY35
PCI Vendor ID:                      0x8086
PCI Vendor Subsystem ID:            0x1d49
IEEE OUI Identifier:                0x5cd2e4
Total NVM Capacity:                 1,600,321,314,816 [1.60 TB]
Unallocated NVM Capacity:           0
Controller ID:                      0
Number of Namespaces:               128
Namespace 1 Size/Capacity:          1,600,321,314,816 [1.60 TB]
Namespace 1 Formatted LBA Size:     512
Namespace 1 IEEE EUI-64:            5cd2e4 c337410100
Local Time is:                      Wed Aug 20 14:53:55 2025 CST
```

![[Pasted image 20250820145505.png]]

https://www.intel.cn/content/www/cn/zh/architecture-and-technology/cloud-inspired-storage-optimized-p4610-brief.html

## Write back

```shell
echo "write back" | tee /sys/block/nvme3n1/queue/write_cache
write back
```

### fsync

```shell
$ sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw --file-fsync-mode=fsync run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

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
    reads/s:                      70830.70
    writes/s:                     47220.30
    fsyncs/s:                     151350.03

Throughput:
    read, MiB/s:                  1106.73
    written, MiB/s:               737.82

General statistics:
    total time:                          10.0085s
    total number of events:              2694166

Latency (ms):
         min:                                    0.00
         avg:                                    0.07
         max:                                   22.49
         95th percentile:                        0.32
         sum:                               196253.47

Threads fairness:
    events (avg/stddev):           134708.3000/935.89
    execution time (avg/stddev):   9.8127/0.01
```

### fdatasync

```shell
$ sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw --file-fsync-mode=fdatasync run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

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
    reads/s:                      75642.49
    writes/s:                     50428.33
    fsyncs/s:                     161625.39

Throughput:
    read, MiB/s:                  1181.91
    written, MiB/s:               787.94

General statistics:
    total time:                          10.0087s
    total number of events:              2877350

Latency (ms):
         min:                                    0.00
         avg:                                    0.07
         max:                                   22.30
         95th percentile:                        0.30
         sum:                               195763.02

Threads fairness:
    events (avg/stddev):           143867.5000/1121.36
    execution time (avg/stddev):   9.7882/0.01
```

## Write through

```shell
echo "write through" | tee /sys/block/nvme3n1/queue/write_cache
write through
```

### fsync

```shell
$ sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw --file-fsync-mode=fsync run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

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
    reads/s:                      115013.61
    writes/s:                     76675.84
    fsyncs/s:                     245606.76

Throughput:
    read, MiB/s:                  1797.09
    written, MiB/s:               1198.06

General statistics:
    total time:                          10.0020s
    total number of events:              4371991

Latency (ms):
         min:                                    0.00
         avg:                                    0.05
         max:                                   27.63
         95th percentile:                        0.28
         sum:                               196991.14

Threads fairness:
    events (avg/stddev):           218599.5500/1174.76
    execution time (avg/stddev):   9.8496/0.00
```

### fdatasync

```shell
$ sysbench fileio --threads=20 --file-total-size=2G --file-test-mode=rndrw --file-fsync-mode=fdatasync run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

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
    reads/s:                      137738.41
    writes/s:                     91825.61
    fsyncs/s:                     294095.31

Throughput:
    read, MiB/s:                  2152.16
    written, MiB/s:               1434.78

General statistics:
    total time:                          10.0073s
    total number of events:              5238732

Latency (ms):
         min:                                    0.00
         avg:                                    0.04
         max:                                   35.65
         95th percentile:                        0.25
         sum:                               196038.35

Threads fairness:
    events (avg/stddev):           261936.6000/2237.26
    execution time (avg/stddev):   9.8019/0.01
```
