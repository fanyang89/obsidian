#ZooKeeper

```yaml
services:
zoo1:
  container_name: zoo1
  image: zookeeper:latest
  restart: unless-stopped
  hostname: zoo1
  network_mode: host
  ports:
    - 2181:2181
  environment:
    ZOO_MY_ID: 1
    ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181
zoo2:
  container_name: zoo2
  image: zookeeper:latest
  restart: unless-stopped
  hostname: zoo2
  network_mode: host
  ports:
    - 2182:2181
  environment:
    ZOO_MY_ID: 2
    ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181
zoo3:
  container_name: zoo3
  image: zookeeper:latest
  restart: unless-stopped
  hostname: zoo3
  network_mode: host
  ports:
    - 2183:2181
  environment:
    ZOO_MY_ID: 3
    ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181
```
