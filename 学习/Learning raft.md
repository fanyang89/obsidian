#consensus #raft

## Prevote

如果网络一直没有恢复，这是没有问题的。但是，如果网络分区恢复，此时，达不到quorum的分区中的节点的term值会远大于能够达到quorum的分区中的节点的term，这会导致能够达到quorum的分区的leader退位（step down）并增大自己的term到更大的term，使集群产生一轮不必要的选举。
Pre-Vote 机制就是为了解决这一问题而设计的，其解决的思路在于不允许达不到 quorum 的分区正常进入投票流程，也就避免了其 term 号的增大。

## 引用

[etcd-raft-3]: https://blog.mrcroxx.com/posts/code-reading/etcdraft-made-simple/3-election "深入浅出 etcd/raft —— 0x03 Raft 选举"
