```
[root@elf-host-172-20-128-77 10:55:20 2023-03-16]$zkCli.sh -server 10.0.19.82:2181
Welcome to ZooKeeper!
JLine support is enabled
[zk: 10.0.19.82:2181(CONNECTING) 0] 
[zk: 10.0.19.82:2181(CONNECTED) 0] ls /zos/service/meta
[z-0000001017, z-0000001018, z-0000001019]
[zk: 10.0.19.82:2181(CONNECTED) 1] get /zos/service/meta/z-0000001017
org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /zos/service/meta/z-0000001017
[zk: 10.0.19.82:2181(CONNECTED) 2] get /zos/service/meta/z-0000001018
10.0.19.80:10100
[zk: 10.0.19.82:2181(CONNECTED) 3] 

[root@elf-host-172-20-128-77 10:56:22 2023-03-16]$zkCli.sh -server 10.0.19.81:2181
Welcome to ZooKeeper!
JLine support is enabled
[zk: 10.0.19.81:2181(CONNECTED) 0] 
[zk: 10.0.19.81:2181(CONNECTED) 0] ls /zos/service/meta
[z-0000001018, z-0000001019, z-0000001020]
[zk: 10.0.19.81:2181(CONNECTED) 1] get /zos/service/meta/z-0000001018
10.0.19.80:10100

[root@elf-host-172-20-128-77 10:57:05 2023-03-16]$zkCli.sh -server 10.0.19.80:2181
Welcome to ZooKeeper!
JLine support is enabled
[zk: 10.0.19.80:2181(CONNECTING) 0] 
[zk: 10.0.19.80:2181(CONNECTED) 0] ls /zos/service/meta
[z-0000001019, z-0000001020, z-0000001021]
```


