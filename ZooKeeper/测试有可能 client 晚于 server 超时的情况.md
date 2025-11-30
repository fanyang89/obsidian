---
title: 测试有可能 client 晚于 server 超时的情况
---

#ZooKeeper

```java
# client

# 创建连接
2022-07-12T00:52:45,690 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /127.0.0.1:54322, server: localhost/127.0.0.1:2181
2022-07-12T00:52:45,700 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1402] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x10003230ece0000, negotiated timeout = 6000

# PING1
2022-07-12T00:52:47,695 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered

# PING2
2022-07-12T00:52:49,704 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered

# PING3 没有发送
2022-07-12T00:52:51,708 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1054] - sendPing() ignored

# Client session timeout
2022-07-12T00:52:53,713 [myid:127.0.0.1:2181] - WARN  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1198] - Client session timed out, have not heard from server in 4003ms for sessionid 0x10003230ece0000
```

```java
# server

# PING1 rsp
2022-07-12T00:52:47,705 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@198] - PING sendResponse()

# PING2 rsp
2022-07-12T00:52:49,709 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@198] - PING sendResponse()

# PING3 rsp
# 应当在 2022-07-12T00:52:51，但是没收到

# Server session 超时
2022-07-12T00:52:51     计时超时
2022-07-12T00:52:57,504 [myid:] - INFO  [SessionTracker:ZooKeeperServer@436] - Expiring session 0x10003230ece0000, timeout of 6000ms exceeded
```

# 测试有可能 client 晚于 server 超时的情况

## PING rsp sleep 3.5s

```java
# client

# 创建连接
2022-07-12T01:06:40,706 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /127.0.0.1:54535, server: localhost/127.0.0.1:2181
2022-07-12T01:06:40,717 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1402] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x100032fbdf40000, negotiated timeout = 6000

# PING1
2022-07-12T01:06:42,713 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:06:44,718 [myid:127.0.0.1:2181] - WARN  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1198] - Client session timed out, have not heard from server in 4001ms for sessionid 0x100032fbdf40000
2022-07-12T01:06:44,720 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1246] - Client session timed out, have not heard from server in 4001ms for sessionid 0x100032fbdf40000, closing socket connection and attempting reconnect
# PING1 超时，重连

# 创建连接
2022-07-12T01:06:46,389 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /127.0.0.1:54536, server: localhost/127.0.0.1:2181
2022-07-12T01:06:46,392 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1402] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x100032fbdf40000, negotiated timeout = 6000
2022-07-12T01:06:48,393 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:06:50,394 [myid:127.0.0.1:2181] - WARN  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1198] - Client session timed out, have not heard from server in 4002ms for sessionid 0x100032fbdf40000
2022-07-12T01:06:50,395 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1246] - Client session timed out, have not heard from server in 4002ms for sessionid 0x100032fbdf40000, closing socket connection and attempting reconnect
2022-07-12T01:06:50,500 [myid:] - ERROR [main-EventThread:ClientCnxn$EventThread@537] - Error while calling watcher
java.lang.NullPointerException: null
	at org.apache.zookeeper.ClientCnxn$EventThread.processEvent(ClientCnxn.java:535) [classes/:?]
	at org.apache.zookeeper.ClientCnxn$EventThread.run(ClientCnxn.java:510) [classes/:?]
2022-07-12T01:06:52,419 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1120] - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2022-07-12T01:06:52,420 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /127.0.0.1:54537, server: localhost/127.0.0.1:2181
2022-07-12T01:06:52,423 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1402] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x100032fbdf40000, negotiated timeout = 6000
2022-07-12T01:06:52,423 [myid:] - ERROR [main-EventThread:ClientCnxn$EventThread@537] - Error while calling watcher
java.lang.NullPointerException: null
	at org.apache.zookeeper.ClientCnxn$EventThread.processEvent(ClientCnxn.java:535) [classes/:?]
	at org.apache.zookeeper.ClientCnxn$EventThread.run(ClientCnxn.java:510) [classes/:?]
2022-07-12T01:06:54,422 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:06:56,426 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:06:58,428 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:07:00,429 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
```

```java
# server

# PING1 rsp 等 3.5s 再发
2022-07-12T01:06:42,723 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@199] - sleep for 3.5s
2022-07-12T01:06:44,724 [myid:] - WARN  [NIOWorkerThread-4:NIOServerCnxn@366] - Unable to read additional data from client sessionid 0x100032fbdf40000, likely client has closed socket
2022-07-12T01:06:46,229 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@201] - sleep for 3.5s, done
2022-07-12T01:06:46,234 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()

2022-07-12T01:06:50,396 [myid:] - WARN  [NIOWorkerThread-8:NIOServerCnxn@366] - Unable to read additional data from client sessionid 0x100032fbdf40000, likely client has closed socket
2022-07-12T01:06:54,427 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:06:56,433 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:06:58,429 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:07:00,430 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
```

## PING rsp sleep 1.8s

```java
# client
2022-07-12T01:13:01,508 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /127.0.0.1:54672, server: localhost/127.0.0.1:2181
2022-07-12T01:13:01,518 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1402] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x1000335313a0000, negotiated timeout = 6000
2022-07-12T01:13:03,519 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:05,334 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:07,337 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:09,338 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:11,342 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:13,345 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:15,350 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:17,353 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:19,355 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
2022-07-12T01:13:21,358 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1057] - sendPing() triggered
```

```java
# server
2022-07-12T01:13:03,524 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@199] - sleep for 1.8s
2022-07-12T01:13:05,330 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@201] - sleep for 1.8s, done
2022-07-12T01:13:05,332 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:07,339 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:09,343 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:11,346 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:13,352 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:15,352 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:17,359 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:19,359 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
2022-07-12T01:13:21,364 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@206] - PING sendResponse()
```

# lastest

```java
# client
2022-07-12T01:27:12,066 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /127.0.0.1:54932, server: localhost/127.0.0.1:2181
2022-07-12T01:27:12,084 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1397] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x100034284fb0000, negotiated timeout = 6000
2022-07-12T01:27:12,085 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 0

2022-07-12T01:27:14,070 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 1986
2022-07-12T01:27:14,070 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1050] - sendPing() triggered
2022-07-12T01:27:14,072 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 1989
2022-07-12T01:27:14,073 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 1989

2022-07-12T01:27:15,883 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 3799
2022-07-12T01:27:15,883 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1050] - sendPing() triggered
2022-07-12T01:27:15,884 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 0
2022-07-12T01:27:15,884 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 1

2022-07-12T01:27:17,885 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 2002
2022-07-12T01:27:17,886 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1050] - sendPing() triggered
2022-07-12T01:27:17,887 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 2003
2022-07-12T01:27:17,888 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 2004

2022-07-12T01:27:19,886 [myid:127.0.0.1:2181] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1181] - idleRecv = 4003
2022-07-12T01:27:19,887 [myid:127.0.0.1:2181] - WARN  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1193] - Client session timed out, have not heard from server in 4003ms for sessionid 0x100034284fb0000
```

```java
# server
2022-07-12T01:27:14,075 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@199] - sleep for 1.8s

2022-07-12T01:27:15,880 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@201] - sleep for 1.8s, done
2022-07-12T01:27:15,882 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@204] - send PING rsp
2022-07-12T01:27:15,884 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@207] - drop PING rsp

2022-07-12T01:27:17,890 [myid:] - INFO  [SyncThread:0:FinalRequestProcessor@207] - drop PING rsp

2022-07-12T01:27:25,442 [myid:] - INFO  [SessionTracker:ZooKeeperServer@436] - Expiring session 0x100034284fb0000, timeout of 6000ms exceeded
```
