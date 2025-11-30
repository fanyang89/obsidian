```bash
fio --name=test --ioengine=libaio --iodepth=16 --rw=randrw --bs=16k --size=4G --fsync=1 --filename=test-file1
fio --name=test --ioengine=libaio --iodepth=16 --rw=randrw --bs=16k --size=4G --fsync=0 --fdatasync=1000 --filename=test-file1
```

## Write back

```bash
fanmi@fanyang-epyc [00:20:32] [/mnt/sysbench]
-> % echo "write back" | sudo tee /sys/block/nvme9n1/queue/write_cache
write back

-> % fio --name=test --ioengine=libaio --iodepth=16 --rw=randrw --bs=16k --size=4G --fsync=1 --filename=test-file1
read: IOPS=719, BW=11.2MiB/s (11.8MB/s)(693MiB/61681msec)
	 lat (usec): min=2362, max=21154, avg=9889.19, stdev=2171.73
write: IOPS=714, BW=11.2MiB/s (11.7MB/s)(689MiB/61681msec); 0 zone resets
	 lat (usec): min=1180, max=22460, avg=10996.18, stdev=2184.29
fsync/fdatasync/sync_file_range:
  sync (msec): min=3, max=743, avg=11.63, stdev= 8.79
```

```bash
-> % fio --name=test --ioengine=libaio --iodepth=16 --rw=randrw --bs=16k --size=4G --fsync=0 --fdatasync=1000 --filename=test-file1
read: IOPS=6537, BW=102MiB/s (107MB/s)(2048MiB/20043msec)
	lat (usec): min=140, max=33073, avg=1266.54, stdev=598.38
write: IOPS=6541, BW=102MiB/s (107MB/s)(2049MiB/20043msec); 0 zone resets
fsync/fdatasync/sync_file_range:
  sync (usec): min=4882, max=13525, avg=7342.96, stdev=1654.48
```

## Write through

```bash
# fio --name=test --ioengine=libaio --iodepth=16 --rw=randrw --bs=16k --size=4G --fsync=1 --filename=test-file1
  read: IOPS=5399, BW=84.4MiB/s (88.5MB/s)(2048MiB/24270msec)
       lat (usec): min=131, max=6543, avg=1517.35, stdev=276.81
	write: IOPS=5401, BW=84.4MiB/s (88.5MB/s)(2049MiB/24270msec); 0 zone resets
	     lat (usec): min=161, max=6482, avg=1406.38, stdev=278.50
fsync/fdatasync/sync_file_range:
  sync (usec): min=505, max=2741.4k, avg=1487.94, stdev=18533.48
```

```bash
# fio --name=test --ioengine=libaio --iodepth=16 --rw=randrw --bs=16k --size=4G --fsync=0 --fdatasync=1000 --filename=test-file1
  read: IOPS=6445, BW=101MiB/s (106MB/s)(2048MiB/20332msec)
		 lat (usec): min=131, max=33183, avg=1260.20, stdev=552.71
	write: IOPS=6448, BW=101MiB/s (106MB/s)(2049MiB/20332msec); 0 zone resets
     lat (usec): min=109, max=16200, avg=1137.64, stdev=584.07
fsync/fdatasync/sync_file_range:
  sync (usec): min=4250, max=15713, avg=6499.18, stdev=1693.12
```