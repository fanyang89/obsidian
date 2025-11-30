#oracle

## mmap 频率

```bash
strace -T -f -p $(pidof osysmond.bin) -e trace=mmap
```

观察到是每 5s 进行一组，每组 1281 次 `mmap`

```
[root@rac1 ~]# grep '11:31:35' out.log | wc -l
1281
[root@rac1 ~]# grep '11:31:40' out.log | wc -l
1281
```

看起来是秒数位为 5 整除的时候，会进行 `mmap`

## 判断 `osysmond` 与已知参数的关系

### 当前参数

```bash
# ./LINUX.X64_193000_grid_home/bin/crsctl get css misscount
CRS-4678: Successful get misscount 60 for Cluster Synchronization Services.

# ./LINUX.X64_193000_grid_home/bin/crsctl get css reboottime
CRS-4678: Successful get reboottime 3 for Cluster Synchronization Services.
```

```bash
# export ORACLE_HOME=$PWD/LINUX.X64_193000_grid_home
# export ORACLE_SID=+ASM1
# ./LINUX.X64_193000_grid_home/bin/sqlplus / as sysasm
SQL> select ksppinm,ksppstvl,ksppdesc from x$ksppi x,x$ksppcv y where x.indx = y.indx and ksppinm='_asm_hbeatiowait';

KSPPINM
--------------------------------------------------------------------------------
KSPPSTVL
--------------------------------------------------------------------------------
KSPPDESC
--------------------------------------------------------------------------------
_asm_hbeatiowait
229
number of secs to wait for PST Async Hbeat IO return
```

### 预期

按照之前的分析，[[osysmond.bin]] 跟 [[ocssd.bin]] 应该有着一样的行为。
试验预期：

- mmap 注入延迟 56s，观察 2min，不会重启
- mmap 注入延迟 57s，观察 2min，会重启
- mmap 注入延迟 60s，观察 2min，会重启

### 试验

在另一台机器上 `ping` 受害者

```bash
for ((;;)); do date; ping 192.168.28.7 -c 1; sleep 1; done
for ((;;)); do date; ping 10.100.101.102 -c 1; sleep 1; done
```

开始注入

```bash
date; /usr/local/bin/strace -f -e inject=mmap:delay_enter=56000000 -p $(pidof osysmond.bin) -o strace-osysmond.log
# start: Mon Aug 12 13:00:54 CST 2024
# end:   Mon Aug 12 13:03:05 CST 2024
# works fine

date; /usr/local/bin/strace -f -e inject=mmap:delay_enter=57000000 -p $(pidof osysmond.bin) -o strace-osysmond.log
# start: Mon Aug 12 13:04:00 CST 2024
# end:   Mon Aug 12 13:05:44 CST 2024
# works fine

date; /usr/local/bin/strace -f -e inject=mmap:delay_enter=61000000 -p $(pidof osysmond.bin) -o strace-osysmond.log
# start: Mon Aug 12 13:05:59 CST 2024
# end:   Mon Aug 12 13:09:58 CST 2024
# works fine
```

```bash
# find / -name "*alert.log"
/home/fanmi/oracle_base/diag/crs/rac1/crs/trace/alert.log
/home/fanmi/oracle_base/diag/asmcmd/user_fanmi/rac1/alert/alert.log
```

```bash
strace -T -t -f -p $(pidof ologgerd) -e write
# 5s 写一次

# write delay 11s
# date; /usr/local/bin/strace -f -e inject=write:delay_enter=11000000 -p $(pidof ologgerd) -o strace-ologgerd.log
Mon Aug 12 15:21:22 CST 2024
/usr/local/bin/strace: Process 93393 attached with 16 threads
[root@rac2 strace]# date
Mon Aug 12 15:21:51 CST 2024

# date; /usr/local/bin/strace -f -e inject=write:delay_enter=15000000 -p $(pidof ologgerd) -o strace-ologgerd.log
```
