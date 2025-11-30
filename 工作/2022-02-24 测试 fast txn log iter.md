## 测试条件

- JNA 实现的 DirectInputStream（DirectIO）
- 一共 250M \* 5 + 100M = 1350M log 文件

```bash
$ ls -lsh ./zk3/version-2 | grep log
250M -rw-r--r-- 1 fanmi fanmi 250M  2月 22 19:17 log.100000001
250M -rw-r--r-- 1 fanmi fanmi 250M  2月 22 19:17 log.c00000001
250M -rw-r--r-- 1 fanmi fanmi 250M  2月 22 19:18 log.c000079a5
250M -rw-r--r-- 1 fanmi fanmi 250M  2月 22 19:18 log.c0000ea15
250M -rw-r--r-- 1 fanmi fanmi 250M  2月 22 19:18 log.c000162e9
100M -rw-r--r-- 1 fanmi fanmi 100M  2月 22 19:17 log.c0001d3b0
```

## 读取测试

### 无注入延迟

- 读取从 zxid=0 开始，不会进行 fast forward
- btrfs
- VMware vmdk on Windows NTFS
- 无注入延迟

| Fast FileTxnIterator    | 5241ms | 5457ms | 5244ms | 5749ms | 5379ms |
| ----------------------- | ------ | ------ | ------ | ------ | ------ |
| Vanilla FileTxnIterator | 5307ms | 5291ms | 5719ms | 5477ms | 5147ms |

### 有注入延迟

- 读取从 zxid=0 开始，不会进行 fast forward
- btrfs on dm on ramdisk
- 有注入延迟

```bash
sudo modprobe brd rd_nr=1 rd_size=10485760 # 10G ramdisk
sudo blockdev --getsize /dev/ram0

# 创建 delayed dm，延迟为 500ms
export RAM_SIZE=$(blockdev --getsize /dev/ram0)
echo "0 $RAM_SIZE delay /dev/ram0 0 500" | sudo dmsetup create delayed

export RAM_SIZE=$(blockdev --getsize /dev/ram0)
echo "0 $RAM_SIZE delay /dev/ram0 0 10" | sudo dmsetup reload delayed
```

```bash
$ sudo fio --name a --filename=/dev/ram0 --bs=4k --rw=randread --ioengine=libaio --direct=1 --iodepth=1 --numjobs=1 --time_based --runtime=10

read: IOPS=681k, BW=2658MiB/s (2787MB/s)(26.0GiB/10001msec)
slat (nsec): min=790, max=256915, avg=896.94, stdev=1169.67
clat (nsec): min=200, max=85042, avg=235.39, stdev=580.46
lat (nsec): min=1040, max=257615, avg=1179.64, stdev=1346.42
clat percentiles (nsec):
|  1.00th=[  211],  5.00th=[  211], 10.00th=[  211], 20.00th=[  221],
| 30.00th=[  221], 40.00th=[  221], 50.00th=[  221], 60.00th=[  221],
| 70.00th=[  231], 80.00th=[  231], 90.00th=[  231], 95.00th=[  241],
| 99.00th=[  282], 99.50th=[  310], 99.90th=[  502], 99.95th=[ 5472],
| 99.99th=[35584]
bw (  MiB/s): min= 2612, max= 2696, per=100.00%, avg=2661.24, stdev=20.66, samples=19
iops        : min=668916, max=690328, avg=681276.58, stdev=5290.14, samples=19
```

```bash
$ sudo fio --name a --filename=/dev/dm-0 --bs=4k --rw=randread --ioengine=libaio --direct=1 --iodepth=1 --numjobs=1 --time_based --runtime=10

a: (g=0): rw=randread, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=1
fio-3.28
Starting 1 process
Jobs: 1 (f=1): [r(1)][100.0%][r=8KiB/s][r=2 IOPS][eta 00m:00s]
a: (groupid=0, jobs=1): err= 0: pid=2838: Thu Feb 24 15:41:40 2022
read: IOPS=1, BW=8123B/s (8123B/s)(80.0KiB/10084msec)
slat (nsec): min=13010, max=81262, avg=19065.55, stdev=15183.01
clat (usec): min=503437, max=506781, avg=504127.94, stdev=705.59
lat (usec): min=503454, max=506863, avg=504147.29, stdev=718.87
clat percentiles (msec):
|  1.00th=[  506],  5.00th=[  506], 10.00th=[  506], 20.00th=[  506],
| 30.00th=[  506], 40.00th=[  506], 50.00th=[  506], 60.00th=[  506],
| 70.00th=[  506], 80.00th=[  506], 90.00th=[  506], 95.00th=[  506],
| 99.00th=[  506], 99.50th=[  506], 99.90th=[  506], 99.95th=[  506],
| 99.99th=[  506]
bw (  KiB/s): min=    7, max=    8, per=88.24%, avg= 7.47, stdev= 0.51, samples=19
iops        : min=    1, max=    2, avg= 1.47, stdev= 0.51, samples=19
```

1ms 延迟

file txn

```bash
Device            r/s     rMB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wMB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dMB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util
dm-0           480.00      0.23     0.00   0.00    2.08     0.50    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    1.00 100.00
```

fast file txn

```

```

## 修改 I/O block size

- ext4
- 一共 250M \* 5 + 100M = 1350M log 文件
- BufferedInputStream + FileInputStream

```bash
2022-02-24T17:47:03,993 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 10236ms
2022-02-24T17:47:05,178 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 1184ms
2022-02-24T17:47:06,263 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 1086ms
2022-02-24T17:47:07,378 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 1114ms
```

```bash
Device            r/s     rMB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wMB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dMB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util
dm-0           954.00    118.86     0.00   0.00    2.05   127.58    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    1.96  99.70
```

大约是 120M/s

- ext4
- DirectInputStream direct=false buf=1M

```bash
Device            r/s     rMB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wMB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dMB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util
dm-0           744.00     93.00     0.00   0.00    2.04   128.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    1.51  85.50

2022-02-24T17:50:54,679 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 12999ms
2022-02-24T17:50:57,831 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 3151ms
2022-02-24T17:51:00,945 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 3114ms
```

## commons-io

```bash
2022-02-24T18:11:43,029 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 10201ms
2022-02-24T18:11:44,088 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 1058ms
2022-02-24T18:11:45,047 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 960ms
2022-02-24T18:11:46,007 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 960ms
2022-02-24T18:11:46,905 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 897ms
2022-02-24T18:11:47,806 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 900ms
2022-02-24T18:11:48,713 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 907ms
2022-02-24T18:11:49,618 [myid:] - INFO  [main:FastFileTxnIterator$TxnLogIterMain@479] - FileTxnIterator took 905ms
```

## vmtouch

```bash
vmtouch -vt `env -i DATA_DIR=$(cat /etc/zookeeper/zoo.cfg  | grep dataDir | awk -F= '{print $2}') bash -c 'ls -t $DATA_DIR/version-2 | grep log | head -n +3 | xargs -I{} echo "$DATA_DIR/version-2/{}"'`

while sleep $(echo 20-`date "+%s"`%20 | bc); do vmtouch -vt `env -i DATA_DIR=$(cat /etc/zookeeper/zoo.cfg  | grep dataDir | awk -F= '{print $2}') bash -c 'ls -t $DATA_DIR/version-2 | grep log | head -n +3 | xargs -I{} echo "$DATA_DIR/version-2/{}"'`; done
```

[[dm-delay 注入磁盘延迟]]
