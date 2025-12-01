⏺ Zookeeper ephemeral 节点机制虽然存在，但不是主要问题：

- 强制断电后，Zookeeper 会在会话超时后自动清理 leader 的 ephemeral 选举节点
- 但是即使 leader 节点被清理，其他节点因为 canElection 返回 false 而不会参与选举

⏺ Update Todos
⎿  ☒ 重新分析强制断电时 HA 切换完全未触发的原因
☒ 检查 election 触发条件和机制
☒ 分析 ping 检测逻辑的缺陷
☒ 检查 Zookeeper ephemeral 节点清理机制

⏺ 总结：强制断电时 HA 切换完全未触发的根本原因

设计缺陷：节点参与选举的条件过于严格

1. canElection 函数要求本地网络接口必须健康 (component.go:380)
2. 强制断电时，其他节点的 ping 检测会失败，因为无法 ping 通故障节点
3. ping 检测失败导致接口被标记为不健康，canElection 返回 false
4. 因此其他节点不会参与选举，即使 leader 的 ephemeral 节点已被 Zookeeper 清理

这是一个逻辑悖论：要检测 leader 故障需要 ping 测试，但 ping 测试失败又阻止了节点参与选举。

解决方案：

- 修改 canElection 逻辑，允许在网络部分故障时仍参与选举
- 或者添加独立的 leader 故障检测机制，不依赖网络接口健康状态
