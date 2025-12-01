Metric 框架概述

ZBS Inspector 使用 metric API v2 框架，主要类型为 Gauge（仪表盘指标），用于监控 ioreroute
服务器的状态。所有 metric 都注册在 "insight" 命名空间下。

核心 Metric 指标

1. zbs_ioreroute_server_status (data_insight_metric_context.h:19)

- 类型: Gauge
- 标签: \_data_ip (chunk 服务器的数据 IP)
- 取值含义:
  - 0 (IOrerouteNormal): ioreroute 正常 - hypervisor 正确路由到本地 SCVM 的数据 IP
  - 1 (IOrerouteNotConnected): ioreroute 未连接 - 没有心跳信号
  - 2 (IOrerouteSwitchAway): ioreroute 工作但不正常 - 路由到管理 IP 或其他 SCVM 的 IP
- 用途: 监控每个 chunk 服务器的 ioreroute 状态

2. zbs_ioreroute_cluster_total_num (data_insight_metric_context.h:23)

- 类型: Gauge
- 标签: \_uuid (集群 UUID)
- 取值: 集群中 ioreroute 服务器的总数
- 用途: 显示集群规模，应等于 chunk 服务器数量

3. zbs_ioreroute_cluster_connected_num (data_insight_metric_context.h:25)

- 类型: Gauge
- 标签: \_uuid (集群 UUID)
- 取值: 有心跳信号的 ioreroute 服务器数量
- 用途: 监控集群中活跃的 ioreroute 服务器数量

4. zbs_ioreroute_cluster_normal_num (data_insight_metric_context.h:27)

- 类型: Gauge
- 标签: \_uuid (集群 UUID)
- 取值: 状态正常的 ioreroute 服务器数量（路由正确）
- 用途: 监控集群中路由配置正确的服务器数量

数据收集机制

数据来源 (insight.proto:9-15)

通过 HTTP PUT 接口接收来自 hypervisor 的状态报告：

- hypervisor_ip: Hypervisor 的 IP 地址
- local_scvm_data_ip: 本地 SCVM 的数据 IP
- local_scvm_manage_ip: 本地 SCVM 的管理 IP
- nfs_target_ip: NFS 目标 IP（实际路由目标）
- ttl: 生存时间计数器，默认 9（90 秒容错）

更新逻辑 (data_insight_metric_context.cc:93-147)

1. 状态判断: 比较 nfs_target_ip 与 local_scvm_data_ip


    - 相等 → 状态为 0（正常）
    - 不相等 → 状态为 2（路由切换）
    - 无心跳 → 状态为 1（未连接）

2. TTL 管理: 每秒递减，归零时清理过期状态
3. Metric 缓存: 动态创建和销毁 metric 实例，避免内存泄漏

监控架构特点

4. 分布式监控: 每个 hypervisor 定期上报其 ioreroute 状态
5. 实时性: TTL 机制确保及时发现故障节点
6. 多维度: 同时提供单节点状态和集群整体统计
7. 标准化: 遵循 Prometheus 格式，便于集成监控平台

这些 metric 主要用于监控 ZBS 存储集群中的网络路由状态，确保 hypervisor 能够正确访问 SCVM
的数据服务，是保障存储系统网络连通性的关键监控指标。
