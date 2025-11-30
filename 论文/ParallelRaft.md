# Raft

- 选举。选举出来的 leader 一定包含之前任期所有已提交的日志；任意任期最多只有一个 leader（可以没有 leader）
- 提交。Raft 不允许出现日志空洞，当一个 log 提交时，也表示它的所有前序 log 已经提交

# MultiRaft

将数据进行 sharding，分为若干个 region。Region 之间可以无序，region 内有序。
![](assets/Pasted%20image%2020230706153108.png)

## 问题

- 如何分片。通常的数据分片算法就是 Hash 和 Range，TiKV 使用的 Range 来对数据进行数据分片。
  - 为什么使用 Range，主要原因是能更好的将相同前缀的 key 聚合在一起，便于 scan 等操作，这个 Hash 是没法支持的，当然，在 split/merge 上面 Range 也比 Hash 好处理很多，很多时候只会涉及到元信息的修改，都不用大范围的挪动数据。
  - Range 有一个问题在于很有可能某一个 Region 会因为频繁的操作成为性能热点。
