## 18.33 meta

```bash
root     19827     1  0 Feb17 ?        01:50:27 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
```

```bash

[root@Node33 15:59:30 ~]$cd /proc/19827
[root@Node33 15:59:49 19827]$cat comm
zbs-metad

[root@Node33 15:59:53 19827]$cd task/
[root@Node33 15:59:56 task]$ls
19827  19828  19837  19838  19839  19840  19841  19842  19843  19844  19845  19852  19853  19854

[root@Node33 15:59:56 task]$cat 19828/cpuset
/zbs/app
[root@Node33 16:00:07 task]$cat 19828/comm
zbs-metad
```

```
[root@Node33 16:00:30 app]$cat tasks  | wc -l
926
[root@Node33 16:00:33 app]$cat tasks | grep 19828
19828
[root@Node33 16:00:35 app]$pwd
/sys/fs/cgroup/cpuset/zbs/app
```

zbs/app tasks 中最大 pid 为 32523

```
[root@Node33 16:01:58 app]$cat /proc/sys/kernel/pid_max
32768
```

```bash
UID        PID  PPID   LWP  C NLWP STIME TTY          TIME CMD
root     19827     1 19827  0   14 Feb17 ?        00:00:00 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19828  0   14 Feb17 ?        00:00:00 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19837  0   14 Feb17 ?        00:00:19 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19838  0   14 Feb17 ?        00:08:39 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19839  0   14 Feb17 ?        00:00:02 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19840  0   14 Feb17 ?        00:24:55 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19841  0   14 Feb17 ?        00:00:05 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19842  0   14 Feb17 ?        00:13:53 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19843  0   14 Feb17 ?        00:33:29 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19844  0   14 Feb17 ?        00:09:52 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19845  0   14 Feb17 ?        00:00:03 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19852  0   14 Feb17 ?        00:00:13 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19853  0   14 Feb17 ?        00:18:02 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
root     19827     1 19854  0   14 Feb17 ?        00:00:57 /usr/sbin/zbs-metad -base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db/ --foreground
```
