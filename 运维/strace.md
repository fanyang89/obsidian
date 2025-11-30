#Linux #观测

## 定性

```bash
strace -T -f -p $(pidof zbs-metad)
```

## 检查进程内某个调用的耗费时间

```bash
# sudo strace -T -f -p $(pidof zbs-metad) -e trace=clock_gettime
strace: Process 3165 attached with 13 threads
[pid  3223] clock_gettime(CLOCK_MONOTONIC_RAW, {2293, 591195630}) = 0
[pid  3223] clock_gettime(CLOCK_MONOTONIC_RAW, {2293, 591359641}) = 0
[pid  3223] clock_gettime(CLOCK_MONOTONIC_RAW, {2293, 591838023}) = 0
```

## 注入延迟

```bash
# inject delay 26s for mmap
date; strace -f -e inject=mmap:delay_enter=26000000 -p $(pidof osysmond.bin) -o osysmond-inject-mmap-26s.log
```
