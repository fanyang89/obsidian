```bash
# 200 * 1M

2023-09-08T16:07:27+08:00 INF ../tcpmon/storage.go:42 > Created initialEpoch=0
2023-09-08T16:07:27+08:00 INF storage_test.go:61 > Before writing alloc=2271032 frees=16953 sys=15041800
# Alloc 2M, sys=14M, free=100k

2023-09-08T16:07:27+08:00 INF ../tcpmon/storage.go:66 > DataStore writer started
2023-09-08T16:07:28+08:00 INF storage_test.go:61 > During writing alloc=114845504 frees=29622 sys=131931432
# alloc=109M, sys=125M, free=280k

2023-09-08T16:07:28+08:00 INF storage_test.go:61 > After writing alloc=218666112 frees=30058 sys=237922616
# alloc=208M, sys=226M, free=280k

2023-09-08T16:07:28+08:00 INF ../tcpmon/storage.go:74 > DataStore writer exited
```

```bash
# 10000 * 1M
2023-09-08T16:05:31+08:00 INF ../tcpmon/storage.go:42 > Created initialEpoch=0
2023-09-08T16:05:31+08:00 INF storage_test.go:61 > Before writing alloc=2259376 frees=17172 sys=15041800
2023-09-08T16:05:31+08:00 INF ../tcpmon/storage.go:66 > DataStore writer started
2023-09-08T16:05:59+08:00 INF storage_test.go:61 > During writing alloc=419071464 frees=274260 sys=1334258232
2023-09-08T16:06:29+08:00 INF storage_test.go:61 > After writing alloc=283936976 frees=566370 sys=1334258232
2023-09-08T16:06:29+08:00 INF ../tcpmon/storage.go:74 > DataStore writer exited
```
