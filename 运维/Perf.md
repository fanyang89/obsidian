#profile #perf #performance #gperftools

```
yum install -y pprof
curl -LO http://192.168.91.19/scripts/perf/flamegraph.pl
```

```bash
pprof --collapsed ./src/zbs_test /tmp/dbc-gperf.data > /tmp/dbc-gperf.data.cbt
./flamegraph.pl --width 2400 --height 32  /tmp/dbc-gperf.data.cbt > /tmp/dbc-gperf.data.svg
curl -T "/tmp/dbc-gperf.data.svg" http://192.168.90.221/f/tmp/

./flamegraph.pl --width 2400 --height 32  /tmp/dbc-gperf.data.cbt > /tmp/dbc-gperf.data2.svg
curl -T "/tmp/dbc-gperf.data2.svg" http://192.168.90.221/f/tmp/
```

```
pprof --collapsed /usr/sbin/zbs-metad status-server-20240423.prof > 1.cbt
./flamegraph.pl --width 1200 --height 16  1.cbt > status-server-20240423.prof.svg
curl -T status-server-20240423.prof.svg http://192.168.91.19:81/tmp/ | cat
```

## lttng 追踪 meta 各项指标

```bash
lttng create
lttng enable-event -u "common:*"
lttng enable-event -u "meta:*"
lttng track -u -p $(pidof zbs-metad)
```

# 检查 tid

```bash
top -Hp $(pidof zbs-metad)
```

# perf

```bash
perf record -g -F 99 -t 5359,6033
```

# gperftools

```
zbs-meta --meta_ip 127.0.0.1 profiler start /root/gperf-$(date +%s).data
pprof --svg ./gperf.data > gperf.svg
```
