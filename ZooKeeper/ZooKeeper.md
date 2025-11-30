#ZooKeeper

# `zoo.cfg` 样例

```
preAllocSize=51200
autopurge.snapRetainCount=3
snapCount=40960
standaloneEnabled=false
syncLimit=4
tickTime=1000
minSessionTimeout=2000
globalOutstandingLimit=10240
initLimit=40
maxClientCnxns=4096
reconfigEnabled=true
fsync.warningthresholdms=1000
skipACL=no
cnxTimeout=1000
dataDir=/var/lib/zookeeper
autopurge.purgeInterval=1
maxSessionTimeout=30000
admin.enableServer=false
4lw.commands.whitelist=*

clientPort=2181
server.1=127.0.0.1:2881:3881
server.2=127.0.0.1:2882:3882
server.3=127.0.0.1:2883:3883
```

```
for((;;)); do date; echo srvr | nc 127.0.0.1 2181 | grep Mode; sleep 1; done
```
