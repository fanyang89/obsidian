#ZooKeeper

# 设计目标

对 local session timeout 移除前后进行测试，检查是否存在双 leader 情况

# 测试用例设计

## 用例 1

- 启动一个 meta 集群 [A B C]
- 对于 A，切断与 ZooKeeper 的连接，进入 local session timeout 状态
  - 如何切断？直接 ifdown？
- 对于 A，在退出 local session timeout 前，连接上 ZooKeeper

整个过程中，检查是否存在两个 meta leader。检查频率 100ms？

# 测试集群设计

每个虚拟机两张网卡，分别属于不同的网段。管理网始终联通。

- ZooKeeper \* 1
- Meta Server \* 2
