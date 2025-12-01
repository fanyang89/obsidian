## Dogfood

### 版本

```
[root@dogfood-idc-elf-99 16:28:44 ~]$ curl -u 'prometheus:HC!r0cks' http://10.255.0.99:9090/api/v1/status/buildinfo
{"status":"success","data":{"version":"2.37.5","revision":"1c2f7554ed6bb4136bfac64fc8e5522e050a987a","branch":"release-2.37","buildUser":"root@yingjie-dev","buildDate":"20240507-10:39:32","goVersion":"go1.21.4"}}
```

```
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+  P COMMAND
27309 root      20   0 2545104 389524  77472 S   1.0  0.1 142:53.94  5 prometheus
```

内存占用约 380M
CPU 占用最高 ~20%，平时在 ~3% 附近

```
$ curl -fsSL -u 'prometheus:HC!r0cks' "http://10.255.0.99:9090//api/v1/query?query=prometheus_tsdb_head_series"
```

```json
{
  "status": "success",
  "data": {
    "resultType": "vector",
    "result": [
      {
        "metric": {
          "__name__": "prometheus_tsdb_head_series",
          "_instance": "10.255.0.100",
          "_job": "prometheus",
          "instance": "10.255.0.100:9090",
          "job": "prometheus"
        },
        "value": [1761122233.363, "73209"]
      },
      {
        "metric": {
          "__name__": "prometheus_tsdb_head_series",
          "_instance": "10.255.0.104",
          "_job": "prometheus",
          "instance": "10.255.0.104:9090",
          "job": "prometheus"
        },
        "value": [1761122233.363, "73209"]
      },
      {
        "metric": {
          "__name__": "prometheus_tsdb_head_series",
          "_instance": "10.255.0.109",
          "_job": "prometheus",
          "instance": "10.255.0.109:9090",
          "job": "prometheus"
        },
        "value": [1761122233.363, "73209"]
      },
      {
        "metric": {
          "__name__": "prometheus_tsdb_head_series",
          "_instance": "10.255.0.85",
          "_job": "prometheus",
          "instance": "10.255.0.85:9090",
          "job": "prometheus"
        },
        "value": [1761122233.363, "73209"]
      },
      {
        "metric": {
          "__name__": "prometheus_tsdb_head_series",
          "_instance": "10.255.0.99",
          "_job": "prometheus",
          "instance": "10.255.0.99:9090",
          "job": "prometheus"
        },
        "value": [1761122233.363, "73209"]
      }
    ]
  }
}
```

内存硬限制 1G

```
$ cat /etc/systemd/system/smtx-obs-prometheus.slice.d/99-svcres.conf
[Slice]
MemoryLimit=107374182
```

```
[root@dogfood-idc-elf-99 16:51:07 ~]$ systemctl status smtx-obs.slice
● smtx-obs.slice
   Loaded: loaded (/etc/systemd/system/smtx-obs.slice; static; vendor preset: disabled)
  Drop-In: /etc/systemd/system/smtx-obs.slice.d
           └─99-svcres.conf
   Active: active since Sat 2025-10-18 04:14:43 CST; 4 days ago
   CGroup: /smtx.slice/smtx-obs.slice
           ├─fluent-bit.service
           │ └─56974 /usr/bin/fluent-bit -c /etc/fluent-bit/fluent-bit.conf
           ├─siren.service
           │ └─29018 /usr/bin/siren
           ├─oscar.service
           │ └─12971 /usr/bin/oscar server
           ├─node-exporter.service
           │ └─6128 /bin/node-exporter --web.listen-address=localhost:9101 --collector.processes --collector.slabinfo --collector.vmstat.fields=^(oom_kil...
           ├─octopus.service
           │ └─6167 /usr/bin/octopus -log.level DEBUG
           ├─smtx-obs-prometheus.slice
           │ └─prometheus.service
           │   └─27309 /usr/bin/prometheus --web.enable-remote-write-receiver --rules.alert.for-grace-period=2m --web.config.file=/usr/share/prometheus/w...
           └─dolphin.service
             └─30072 /usr/bin/dolphin
```

## ArchLinux

### 版本

```
fanmi@vm-arch [16:29:18] [~/workspace/many-metrics] [main *]
-> % task get-version
task: [get-version] curl http://127.0.0.1:9090/api/v1/status/buildinfo
{"status":"success","data":{"version":"3.6.0","revision":"3.6.0","branch":"tarball","buildUser":"someone@builder","buildDate":"20250923-12:21:46","goVersion":"go1.25.1 X:nodwarf5"}}%
```

内存占用 344M

## Rate 占用

Initial RSS:

```
# index=1
rate(fires_total{index="0"}[1m])
# Result RSS: 404M
```

```
# index=1~10
rate(fires_total{index=~"^(10|[1-9])$"}[1m])
# Result RSS: 426M
```

```
# index=1~100
rate(fires_total{index=~"^(100|[1-9][0-9]|[1-9])$"}[1m])
# Result RSS: 426M
```

```
# index=1~1000
rate(fires_total{index=~"^(1000|[1-9][0-9]{2}|[1-9][0-9]|[1-9])$"}[1m])
# Result RSS: 450M
```

```
# index=1~10000
rate(fires_total{index=~"^(10000|[1-9][0-9]{0,3})$"}[1m])
# Result RSS: ~725M => 704M
```

```
# index=1~20000
rate(fires_total{index=~"^(20000|1[0-9]{4}|[1-9][0-9]{0,3})$"}[1m])
# Result RSS: 1178M, 一段时间后自动回落到 358M
# Diff 1w,   per metric 0.0453M = 45.3K
# Diff 1k,   per metric 0.0383M
# Diff 0.1K, per metric 0.0377M
```

1G 内存限制，估计值，(2w - 4450) ~= 1.5w counter metric

## 目前 ZBS 多少 gauge？

```bash
# curl -u 'prometheus:HC!r0cks' -G 'http://10.255.0.99:9090/api/v1/query' --data-urlencode 'query=count({__name__=~"zbs_.+"})'
{"status":"success","data":{"resultType":"vector","result":[{"metric":{},"value":[1761187342.159,"26214"]}]}}
```

目前一个节点，大约承载 7.3w metrics

![[attachments/a23eb1ebc18c7d6c335987e7b553fc10_MD5.jpg]]

- `chunk-exporter.zbs_chunk`
- `meta-exporter.basic`
