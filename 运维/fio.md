#fio

## 预热

```bash
export DEVICE_NAME=/dev/sdx
fio --name a --filename=$DEVICE_NAME --bs=256k --rw=write --ioengine=libaio --direct=1 --iodepth=32 --numjobs=1 --size=100%

export DEVICE_NAME=/mnt/data/test-file1
fio --name a --filename=$DEVICE_NAME --bs=256k --rw=write --ioengine=io_uring --direct=1 --iodepth=128 --numjobs=1 --size=100G
```

## IOPS

```bash
export DEVICE_NAME=/dev/sdx
fio --name a --filename=$DEVICE_NAME --bs=4k --rw=randread --ioengine=libaio --direct=1 --iodepth=128 --numjobs=1 --size=100%
fio --name a --filename=$DEVICE_NAME --bs=4k --rw=randread --ioengine=libaio --direct=1 --iodepth=128 --numjobs=1 --runtime=999 --time_based
```

## 顺序

```bash
export DEVICE_NAME=/dev/sdx
fio --name a --filename=$DEVICE_NAME --bs=256k --rw=read --ioengine=libaio --direct=1 --iodepth=1 --numjobs=1 --time_based --runtime=60s

fio --name a --filename=/dev/vdb --bs=4k --rw=randwrite --ioengine=libaio --direct=1 --iodepth=128 --numjobs=1 --time_based --runtime=60s
```

## iSCSI

```bash
zbs-iscsi target create fanyang-iscsi --replica_num 1
zbs-iscsi lun create fanyang-iscsi 10 10 # LUN SIZE(in GiB)

iscsiadm -m discovery -t sendtargets -p 127.0.0.1:3260
iscsiadm -m discovery -t iqn.2016-02.com.smartx:system:fanyang-iscsi -p 127.0.0.1:3260
iscsiadm -m node -T iqn.2016-02.com.smartx:system:fanyang-iscsi -p 127.0.0.1:3260 --login
iscsiadm -m session -P 3
lsscsi -it

iscsiadm -m node -T iqn.2016-02.com.smartx:system:fanyang-iscsi -p 127.0.0.1:3260 --logout
```

```bash
# 检查 SCSI PR
sg_persist --in --report-capabilities /dev/sdh
```

```bash
fio --name=test --bssplit=4k/50:252k/50 --rw=write --iodepth=16 --ioengine=libaio --direct=1 --time_based=1 --runtime=600 --filename=/dev/sdh
fio --name=test --bs=4k --rw=randwrite --iodepth=128 --ioengine=libaio --direct=1 --time_based=1 --runtime=600 --filename=/dev/sdi
```

```
awk -v max_sum=0 -v max_cpu=0 'NR > 1 {sum=0; for (i=2; i<=NF; i++) sum+=$i; if (sum > max_sum) {max_sum = sum; max_cpu = $1}} END {sub(/:/,"",max_cpu); print "cpu: ", max_cpu, "irq: ", max_sum}' /proc/softirqs
```

```
awk -v top_n=5 'NR==1 {for (i=2; i<=NF; i++) irq[i]=$i} NR > 1 {sum=0; for (i=2; i<=NF; i++) sum+=$i; cpu_sum[$1]=sum; for (i=2; i<=NF; i++) irq_sum[$1" "$i]+=$i} END {for (cpu in cpu_sum) {sub(/:/,"",cpu); print "cpuset:", cpu, "softirq total count:", cpu_sum[cpu]; for (i=2; i<=top_n+1; i++) {max_sum=0; max_irq=""; for (irq in irq_sum) {split(irq, arr, " "); if (arr[1] == cpu && irq_sum[irq] > max_sum) {max_sum=irq_sum[irq]; max_irq=arr[2]}} print "  softirq:", max_irq, "count:", max_sum; delete irq_sum[cpu" "max_irq]}}}' /proc/softirqs
```
