```bash
root@yl-readtest:/mnt/data# findmnt /mnt/data/
TARGET    SOURCE    FSTYPE OPTIONS
/mnt/data /dev/sda1 xfs    rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota
```

```bash
root@yl-readtest:/mnt/data# cat /sys/block/sda/queue/write_cache
write through
```

```bash
# 128 个 16M 文件

root@yl-readtest:/mnt/data # ls -lah
total 2.1G
drwxr-xr-x. 2 root root 8.0K Aug 19 12:45 .
drwxr-xr-x. 3 root root 4.0K Aug 18 16:43 ..
-rw-r--r--. 1 root root  16M Aug 19 12:48 random-read-write.0.0
-rw-r--r--. 1 root root  16M Aug 19 12:48 random-read-write.0.1
-rw-r--r--. 1 root root  16M Aug 19 12:48 random-read-write.0.10
-rw-r--r--. 1 root root  16M Aug 19 12:48 random-read-write.0.100
-rw-r--r--. 1 root root  16M Aug 19 12:48 random-read-write.0.101
-rw-r--r--. 1 root root  16M Aug 19 12:48 random-read-write.0.102

root@yl-readtest:/mnt/data # ls | wc -l
128
```

```bash
# 可以观察到 fsync 有一个超大的长尾

root@yl-readtest:/mnt/data# fio --name=random-read-write --ioengine=sync --directory=./ --readwrite=randrw --bs=16k --size=2G --numjobs=1 --rwmixread=60 --nrfiles=128 --fsync=100 --group_reporting --time_based --runtime=10
random-read-write: (g=0): rw=randrw, bs=(R) 16.0KiB-16.0KiB, (W) 16.0KiB-16.0KiB, (T) 16.0KiB-16.0KiB, ioengine=sync, iodepth=1
fio-3.37
Starting 1 process
random-read-write: Laying out IO files (128 files / total 2048MiB)
Jobs: 1 (f=128): [m(1)][100.0%][r=56.1MiB/s,w=38.4MiB/s][r=3589,w=2457 IOPS][eta 00m:00s]
random-read-write: (groupid=0, jobs=1): err= 0: pid=14463: Tue Aug 19 12:55:59 2025
  read: IOPS=3703, BW=57.9MiB/s (60.7MB/s)(579MiB/10001msec)
    clat (usec): min=93, max=2090, avg=221.09, stdev=55.34
     lat (usec): min=93, max=2090, avg=221.22, stdev=55.34
    clat percentiles (usec):
     |  1.00th=[  145],  5.00th=[  157], 10.00th=[  165], 20.00th=[  176],
     | 30.00th=[  186], 40.00th=[  196], 50.00th=[  212], 60.00th=[  231],
     | 70.00th=[  247], 80.00th=[  265], 90.00th=[  281], 95.00th=[  314],
     | 99.00th=[  359], 99.50th=[  420], 99.90th=[  465], 99.95th=[  652],
     | 99.99th=[ 1336]
   bw (  KiB/s): min=53408, max=65344, per=100.00%, avg=59277.47, stdev=3434.39, samples=19
   iops        : min= 3338, max= 4084, avg=3704.84, stdev=214.65, samples=19
  write: IOPS=2465, BW=38.5MiB/s (40.4MB/s)(385MiB/10001msec); 0 zone resets
    clat (nsec): min=7090, max=75291, avg=10700.20, stdev=3640.66
     lat (nsec): min=7267, max=75541, avg=10916.89, stdev=3687.04
    clat percentiles (nsec):
     |  1.00th=[ 7328],  5.00th=[ 7584], 10.00th=[ 7712], 20.00th=[ 8032],
     | 30.00th=[ 8640], 40.00th=[ 9408], 50.00th=[ 9920], 60.00th=[10432],
     | 70.00th=[11072], 80.00th=[11968], 90.00th=[14272], 95.00th=[18304],
     | 99.00th=[23680], 99.50th=[27264], 99.90th=[38656], 99.95th=[48896],
     | 99.99th=[60160]
   bw (  KiB/s): min=35648, max=44736, per=100.00%, avg=39525.05, stdev=2599.02, samples=19
   iops        : min= 2228, max= 2796, avg=2470.32, stdev=162.44, samples=19
  lat (usec)   : 10=20.41%, 20=18.31%, 50=1.23%, 100=0.03%, 250=42.88%
  lat (usec)   : 500=17.10%, 750=0.02%, 1000=0.01%
  lat (msec)   : 2=0.01%, 4=0.01%
  fsync/fdatasync/sync_file_range:
    sync (nsec): min=1793, max=21423k, avg=2139779.61, stdev=3062927.35
    sync percentiles (usec):
     |  1.00th=[    3],  5.00th=[  212], 10.00th=[  343], 20.00th=[  486],
     | 30.00th=[  635], 40.00th=[  824], 50.00th=[  996], 60.00th=[ 1287],
     | 70.00th=[ 1696], 80.00th=[ 2966], 90.00th=[ 5604], 95.00th=[ 8979],
     | 99.00th=[17433], 99.50th=[18744], 99.90th=[21365], 99.95th=[21365],
     | 99.99th=[21365]
  cpu          : usr=1.69%, sys=11.46%, ctx=41695, majf=0, minf=15
  IO depths    : 1=101.1%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=37039,24660,0,656 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
   READ: bw=57.9MiB/s (60.7MB/s), 57.9MiB/s-57.9MiB/s (60.7MB/s-60.7MB/s), io=579MiB (607MB), run=10001-10001msec
  WRITE: bw=38.5MiB/s (40.4MB/s), 38.5MiB/s-38.5MiB/s (40.4MB/s-40.4MB/s), io=385MiB (404MB), run=10001-10001msec

Disk stats (read/write):
  sda: ios=36602/18603, sectors=1171264/646126, merge=0/0, ticks=7354/18357, in_queue=25711, util=86.14%
```

检查 SCSI FLUSH 命令耗时：

```bash
root@yl-readtest:/mnt/data# perf stat -r 1000 --null sg_sync /dev/sda
 Performance counter stats for 'sg_sync /dev/sda' (1000 runs):
         0.0008765 +- 0.0000313 seconds time elapsed  ( +-  3.58% )
```

![[Pasted image 20250819130641.png]]

Guest OS 设置的读写比是 3:2，实际上变成了 13:1
