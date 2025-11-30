#oracle

```bash
# blktrace -d /dev/sdb -o - | blkparse -i - | grep ocssd.bin

  8,16   1       36     4.023008819  5399  Q   R 262148 + 1 [ocssd.bin]
  8,16   1       37     4.023012275  5399  G   R 262148 + 1 [ocssd.bin]
  8,16   1       38     4.023013110  5399  I   R 262148 + 1 [ocssd.bin]
  8,16   1       39     4.023021723   526  D   R 262148 + 1 [kworker/1:1H]
  8,16   1       40     4.023371723     0  C   R 262148 + 1 [0]

  8,16   1       41     4.023384469  5399  Q   R 262147 + 1 [ocssd.bin]
  8,16   1       42     4.023385471  5399  G   R 262147 + 1 [ocssd.bin]
  8,16   1       43     4.023385898  5399  I   R 262147 + 1 [ocssd.bin]
  8,16   1       44     4.023390278   526  D   R 262147 + 1 [kworker/1:1H]
  8,16   1       45     4.023596054     0  C   R 262147 + 1 [0]

  8,16   0       34     4.022757683  5396  Q  WS 262161 + 1 [ocssd.bin]
  8,16   0       35     4.022762224  5396  G  WS 262161 + 1 [ocssd.bin]
  8,16   0       36     4.022763229  5396  P   N [ocssd.bin]
  8,16   0       37     4.022763796  5396 UT   N [ocssd.bin] 1
  8,16   0       38     4.022764446  5396  I  WS 262161 + 1 [ocssd.bin]
  8,16   0       39     4.022773375     8  D  WS 262161 + 1 [kworker/0:1H]
  8,16   0       40     4.023286452     0  C  WS 262161 + 1 [0]
```

这一行日志表示：在 4.023013110 秒时，进程 `ocssd.bin`（其 PID 为 5399）在 CPU 1 上发起了一个读请求到设备 `/dev/sdb`。这个请求从扇区 262148 开始，涉及 1 个扇区

- **Q (Queue)**：表示一个请求已经被添加到队列中。这通常是因为一个进程发起了一个 I/O 操作。
- **G (Get Request)**：表示内核已经从请求队列中获取了一个请求，并且准备开始处理它。
- **P (Plug)**：表示设备已经被 "插入"，即将开始一个新的请求队列。在某些情况下，内核可能会延迟一部分 I/O 请求的处理，以便能够将它们组合在一起并一次性处理，这样可以提高效率。"插入" 就是这个过程的开始。
- **U (Unplug)**：这表示设备被 "unplugged"，或者说 I/O 请求队列已经准备好被设备处理了。这通常在 I/O 请求队列中的请求达到一定数量，或者达到了特定的超时时间后发生。
- **UT (Unplug Timer)**：这个事件表示内核已经启动了一个定时器，当定时器到期时，如果之前被 "插入" 的请求队列中还有未处理的请求，那么这些请求就会被 "拔出"，即开始被处理。
- **I (Insert)**：这表示一个新的请求被插入到 I/O 调度器的请求队列中。
- **D (Dispatch)**：这个事件表示一个请求已经从请求队列中被移除，即开始被设备处理。这通常意味着请求已经被发送到设备驱动程序，或者已经被发送到硬件设备。
- **C (Complete)**：这个事件表示一个请求已经被完成。这通常意味着设备已经完成了请求的处理，数据已经被读取或写入，或者错误已经被返回。

![|500](assets/Pasted%20image%2020230903112738.png)

```
5399  pread64(258, "ssLckcoT\1\4\0\0\0\2\0\0\0\0\0\0\0\0\0\0@\240\0\0\0\2\0\0"..., 512, 134219776) = 512

5399  pread64(258,  <unfinished ...>
5399  <... pread64 resumed>"ssLcofnI\1\4\0\0\30\0\0\0\30\0\20\0(\0!\0d\225(vBwO\201"..., 512, 134219264) = 512

5396  pwrite64(256, "etoV\1\0\0\0\1\4\v\2\0\0\0\0rac2\0\0\0\0\0\0\0\0\0\0\0\0"..., 512, 134226432 <unfinished ...>
5396  <... pwrite64 resumed>)           = 512
```

```c
ssize_t pread (int fd,       void *buf, size_t count, off_t offset);
ssize_t pwrite(int fd, const void *buf, size_t count, off_t offset);
```

```
[root@rac2 ~]# pidof ocssd.bin
5174
[root@rac2 ~]# date; strace -f -e inject=pwrite64:delay_enter=204000000 -p $(pidof ocssd.bin) -e inject=pread64:delay_enter=204000000 -o ocssd-inject-io-204s.log
2023年 09月 03日 星期日 11:58:52 CST
strace: Process 5174 attached with 18 threads
strace: Process 6386 attached
strace: dispatch_event: pid 5356 has delayed wait data set already
strace: dispatch_event: pid 5354 has delayed wait data set already
[root@rac2 ~]#
[root@rac2 ~]#
[root@rac2 ~]# pidof ocssd.bin
6561
```

![|800](assets/Pasted%20image%2020230903121332.png)

```
[root@rac2 ~]# date; strace -f -e inject=pread64:delay_enter=204000000 -p $(pidof ocssd.bin) -e inject=pwrite64:delay_enter=204000000 -o ocssd-inject-io-204s.log 2>&1 | xargs -L 1 echo `date +'[%Y-%m-%d %H:%M:%S]'` $1
2023年 09月 03日 星期日 12:14:41 CST
[2023-09-03 12:14:41] strace: Process 5240 attached with 18 threads
[2023-09-03 12:14:41] strace: Process 6987 attached
[2023-09-03 12:14:41] strace: dispatch_event: pid 5421 has delayed wait data set already
[2023-09-03 12:14:41] strace: dispatch_event: pid 5419 has delayed wait data set already

 7302 Sun Sep  3 12:18:08 2023 /u01/app/11.2.0/grid/bin/ocssd. bin

elapsed: 3m27s =
```
