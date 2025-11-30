三副本 target

```
cgexec -g cpuset:. taskset -c 28
```

```bash
fio --name=scsi-flush-test --filename=/dev/sdg --ioengine=psync --sync=1 --rw=randwrite --bs=4k --iodepth=1 --size=10G --group_reporting --runtime=120 --time_based
```

```
# 未预热
scsi-flush-test: (groupid=0, jobs=1): err= 0: pid=1698338: Tue Aug 19 10:24:50 2025
  write: IOPS=45, BW=184KiB/s (188kB/s)(932KiB/5067msec)
    clat (usec): min=13683, max=22068, avg=21738.48, stdev=1248.05
     lat (usec): min=13684, max=22070, avg=21739.52, stdev=1248.04
```

```
^Cbs: 1 (f=1): [w(1)][9.2%][r=0KiB/s,w=184KiB/s][r=0,w=46 IOPS][eta 01m:49s]
fio: terminating on signal 2

scsi-flush-test: (groupid=0, jobs=1): err= 0: pid=1709007: Tue Aug 19 10:28:49 2025
  write: IOPS=45, BW=183KiB/s (187kB/s)(2152KiB/11771msec)
    clat (usec): min=10337, max=22044, avg=21872.90, stdev=869.11
     lat (usec): min=10338, max=22045, avg=21873.95, stdev=869.10
    clat percentiles (usec):
     |  1.00th=[17957],  5.00th=[21890], 10.00th=[21890], 20.00th=[21890],
     | 30.00th=[21890], 40.00th=[21890], 50.00th=[21890], 60.00th=[21890],
     | 70.00th=[21890], 80.00th=[21890], 90.00th=[21890], 95.00th=[22152],
     | 99.00th=[22152], 99.50th=[22152], 99.90th=[22152], 99.95th=[22152],
     | 99.99th=[22152]
   bw (  KiB/s): min=  176, max=  184, per=100.00%, avg=182.61, stdev= 3.10, samples=23
   iops        : min=   44, max=   46, avg=45.65, stdev= 0.78, samples=23
  lat (msec)   : 20=2.04%, 50=97.96%
  cpu          : usr=0.03%, sys=0.05%, ctx=538, majf=0, minf=8
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,538,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
  WRITE: bw=183KiB/s (187kB/s), 183KiB/s-183KiB/s (187kB/s-187kB/s), io=2152KiB (2204kB), run=11771-11771msec

Disk stats (read/write):
  sdg: ios=23/531, merge=0/0, ticks=8/11608, in_queue=11161, util=99.20%
```

