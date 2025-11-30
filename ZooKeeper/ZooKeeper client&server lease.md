#ZooKeeper

# 思想

如果由于网络的延迟等原因，导致对端在接收一个 renew lease 的请求时实际已经过去了一段时间，那么会导致对端 lease 的延长的时间不是服务端预期的。

# TODO

- 检查 sessionsWithTimeout 持久化后重新加载，超时是重新计算，还是延续之前的时间
- 检查 renew session 是否存在 reply 延迟到达对端导致 session 被异常延长

# 参考资料

- http://jira.smartx.com/browse/ZBS-13135
  - http://gerrit.smartx.com/c/zbs/+/30935
- https://jimmy2angel.github.io/2019/01/12/Zookeeper-Session%E6%9C%BA%E5%88%B6/

# ZooKeeper Session 机制

一个常规意义上的 session 机制应当包括几个点：

- 创建（create）
- 续期（extend/renew）
- 过期（expire）

## 创建

```java
public void processConnectRequest(ServerCnxn cnxn, ByteBuffer incomingBuffer) throws IOException {
```

## 过期

TODO

## 续期

Client 需要定期发送 PING 请求以保持 session 的有效。在 $\frac{1}{3} \cdot recv\_timeout$ 处发送一个 PING 请求。
假定是一个 Follower 收到 PING 请求。

# Java Client

```java
private int readTimeout;
private int connectTimeout;

connectTimeout = sessionTimeout / hostProvider.size();
readTimeout = sessionTimeout * 2 / 3;
```

# C Client

```
        // We only allow 1/3 of our timeout time to expire before sending
        // a PING
        if (is_connected(zh)) {
            send_to = zh->recv_timeout/3 - idle_send;
            if (send_to <= 0) {
                if (zh->sent_requests.head == 0) {
                    rc = send_ping(zh);
                    if (rc < 0) {
                        LOG_ERROR(LOGCALLBACK(zh), "failed to send PING request (zk retcode=%d)",rc);
                        return api_epilog(zh,rc);
                    }
                }
                send_to = zh->recv_timeout/3;
            }
        }
```

# 原始需求描述

http://jira.smartx.com/browse/ZBS-13135
http://gerrit.smartx.com/c/zbs/+/30935

libmeta RPC timeout：
Meta server RPC timeout：

```c++
static const int kDefaultConnectTimeoutMs = 200;
DEFINE_int32(default_meta_connect_timeout_ms, zbs::kDefaultConnectTimeoutMs, "Default meta connect timeout");

class Meta {
  public:
    struct Options {
        Options(uint64_t connect_timeout_ms = FLAGS_default_meta_connect_timeout_ms,
                uint64_t rpc_timeout_ms = 10000,
                uint64_t slow_rpc_timeout_ms = 60000,
                uint64_t clear_resource_interval_ms = 180 * 1000,
                uint64_t inactive_lease_interval_s = 3600)
```

对于 libmeta 来说，timeout 如下：

- connect_timeout_ms = 0.2s
- rpc_timeout_ms = 10s
- slow_rpc_timeout_ms = 60s
- clear_resource_interval_ms = 180s
- inactive_lease_interval_s = 3.6s

1. Follower 发送 KeepAlive 请求，以大概 12s - 2.5s=9.5s 为 timeout；
2. master 接收到请求，timer 在 7s 后发送 KeepAlive 回复，同时会重置 timer 超时为 7s；
3. 如果 KeepAlive 回复网络延迟很大，但是没有超过 Follower KeepAlive 超时（9.5s），那么 follower 认为 lease 还正常；
   - 分支 1：master timer 在 7s 内发现还没收到 KeepAlive 请求，状态是 SessionState :: WAIT_KEEPALIVE，那么会重置 timer 超时为 12s；这种情况应该没有问题
   - 分支 2：master timer 如果卡了比较长时间（超过 12s）才触发
     - 发现 expired 了触发 HandleExpire，如果 follower 的 KeepAlive 还没有发过来，那么 HandleExpire 会直接更新 state 为 expired，这种情况下 follower 没法感知到自己已经 expired 了。

问题主要是 分支 1，假设 Session follower 发送 Keepalive 请求，设置的 timeout 是 7s，Master 发送回复的时候，设置 lease 在 12s 后过期，同时授予 Follower 一个新的 lease，因为网络问题， Follower 收到的时候是 6s 之后。
这个时候 Master 处已经认为 lease 要在 12s- 6s 之后过期了。即 Master 认为 Lease 的有效期实际上只有 6s了。
而 Follower 使用了收到的 keepalive 回复中的 12s 延迟了自己的 lease 有效期。
那此时 Follower 的 Lease 生命周期就比 Master 长，如果此时断开了链接。 就会发生 Master 认为 Lease 失效，而 Follower 认为 Lease 有效的时间窗口。
在 Follower 设置自己 Lease 的生命周期的时候是减去一个固定的 2.5s 的 nerwork 延迟，这里包含了一个网络延迟一定不会超过 2.5s 的假设。这个可能不够强。

如果 follower 在 Meta 的 回复 +6s 的时候收到心跳回复，那么原来的代码会设置他的超时时间是 回复 + 6s + 12s -2.5s 。
那就是回复 + 15.5 s 这比 Meta 的回复 + 12s 要长了。
主要是之前试图用 2.5s 的静态网络延迟覆盖 Meta 向 chunk 回复的实际延迟。
如果实际延迟比 2.5s 来的大，就会出现这个不一致窗口。
Follower 先于 Meta 超时是没有问题的，晚于 Meta 是不可以的。

# Day N

```
2022-07-07T20:30:10,493 [myid:127.0.0.1:11228] - INFO  [main-SendThread(127.0.0.1:11228):ClientCnxn$SendThread@1050] - client sendPing()
```

```bash
2022-07-07T20:52:18,776 [myid:] - INFO  [CommitProcWorkThread-1:LearnerSessionTracker@121] - Committing global session 0x3000c361a120000
2022-07-07T20:52:18,776 [myid:] - INFO  [CommitProcWorkThread-1:LearnerSessionTracker@121] - Committing global session 0x3000c361a120000
2022-07-07T20:52:18,779 [myid:127.0.0.1:11228] - INFO  [main-SendThread(127.0.0.1:11228):ClientCnxn$SendThread@1395] - Session establishment complete on server localhost/127.0.0.1:11228, sessionid = 0x3000c361a120000, negotiated timeout = 6000
2022-07-07T20:52:18,793 [myid:] - INFO  [main:LeaderSessionTrackerTest@175] - znode /zk3 created, sessionid=0x3000c361a120000

2022-07-07T20:52:20,735 [myid:127.0.0.1:11228] - INFO  [main-SendThread(127.0.0.1:11228):ClientCnxn$SendThread@1241] - Unable to read additional data from server sessionid 0x3000c361a120000, likely server has closed socket, closing socket connection and attempting reconnect

2022-07-07T20:52:26,927 [myid:127.0.0.1:11228] - INFO  [main-SendThread(127.0.0.1:11228):ClientCnxn$SendThread@1241] - Unable to read additional data from server sessionid 0x3000c361a120000, likely server has closed socket, closing socket connection and attempting reconnect

2022-07-07T20:52:28,405 [myid:127.0.0.1:11228] - INFO  [main-SendThread(127.0.0.1:11228):ClientCnxn$SendThread@1395] - Session establishment complete on server localhost/127.0.0.1:11228, sessionid = 0x3000c361a120000, negotiated timeout = 6000

2022-07-07T20:52:37,128 [myid:] - INFO  [NIOWorkerThread-13:QuorumZooKeeperServer@157] - Submitting global closeSession request for session 0x3000c361a120000
2022-07-07T20:52:37,252 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@524] - EventThread shut down for session: 0x3000c361a120000
2022-07-07T20:52:37,252 [myid:] - INFO  [main:ZooKeeper@1422] - Session: 0x3000c361a120000 closed

# 存活时间：37 - 18 = 19s
```
