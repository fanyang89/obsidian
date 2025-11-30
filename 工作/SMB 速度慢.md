![[Pasted image 20250710193143.png]]

```
10.1.2.111 -> 10.1.2.3  Create request file
10.1.2.3 -> 10.1.2.111  Create response file
elapsed = 23.2ms

10.1.2.111 -> 10.1.2.3  Get info request
10.1.2.3 -> 10.1.2.111  Get info response
elapsed = 7.2ms

10.1.2.111 -> 10.1.2.3  Read request
10.1.2.3 -> 10.1.2.111  Read response status=pending
10.1.2.3 -> 10.1.2.111  Read response
elapsed = 15.2ms

10.1.2.111 -> 10.1.2.3  Close request file
10.1.2.3 -> 10.1.2.111  Close response
elapsed = 7.3ms

total elapsed = 56ms
```

```
explorer 200s
```

```
(0) > rclone copy -P --transfers 1 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 175.148 KiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:      1m52.0s

(0) > rclone copy -P --transfers 1 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 178.353 KiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:      1m54.5s
```

```
(0) > rclone copy -P --transfers 4 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 837.461 KiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:        24.2s

(0) > rclone copy -P --transfers 4 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 800.653 KiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:        25.3s
```

```
(0) > rclone copy -P --transfers 8 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 1.392 MiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:        14.2s

(0) > rclone copy -P --transfers 8 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 1.318 MiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:        15.0s
```

```
(0) > rclone copy -P --transfers 12 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 1.880 MiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:        10.5s

(0) > rclone copy -P --transfers 12 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 1.732 MiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:        11.4s
```

```
(0) > rclone copy -P --transfers 16 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 2.175 MiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:         9.2s

(0) > rclone copy -P --transfers 16 zspace:/156XXXX5589/tmp/file_srcdir D:\tmp1
Transferred:       19.531 MiB / 19.531 MiB, 100%, 2.156 MiB/s, ETA 0s
Transferred:         5000 / 5000, 100%
Elapsed time:         9.3s
```
