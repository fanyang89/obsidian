表 2：核心计算单元规格对比

| 指标                    | 华为 Ascend 910C                  | NVIDIA Blackwell B200           |
| ----------------------- | --------------------------------- | ------------------------------- |
| 架构                    | Ascend (2024)                     | Blackwell                       |
| 工艺节点                | 7nm 级                            | TSMC 4NP (4nm 级)               |
| 晶体管数量              | 未公开                            | 2080 亿                         |
| 裸片/小芯片设计         | 双裸片 (Dual-Die) 封装            | 双小芯片 (Dual-Chiplet) 设计    |
| FP16/BF16 TFLOPS (稠密) | 752                               | 2,250 (2.25 PFLOPS)             |
| INT8/FP8 TFLOPS (稠密)  | 1,504 (INT8)                      | 4,500 (4.5 PFLOPS)              |
| FP4 TFLOPS (稠密)       | 不支持                            | 9,000 (9 PFLOPS)                |
| 片上 HBM 容量           | 128 GB HBM                        | 192 GB HBM3e                    |
| HBM 带宽                | 3.2 TB/s                          | 8 TB/s                          |
| 裸片/小芯片间带宽       | 540 GB/s (双向)                   | 10 TB/s (双向)                  |
| 集成 CPU                | 鲲鹏 (Kunpeng) CPU (在节点级集成) | Grace CPU (在 Superchip 级集成) |
| 最大 TDP                | ~750 W (估算)                     | 1000 W                          |

数据来源: 1

表 4：MoE 推理性能基准测试摘要 (DeepSeek-R1)

| 平台/硬件                            | 阶段    | 吞吐量 (tokens/s 每加速器) | 计算效率 (tokens/s/TFLOPS) | 关键驱动因素                                    |
| ------------------------------------ | ------- | -------------------------- | -------------------------- | ----------------------------------------------- |
| CloudMatrix-Infer on CloudMatrix 384 | Prefill | 6,688                      | 4.45 (INT8)                | 大规模专家并行 (EP32), 混合并行, UB 总线        |
| CloudMatrix-Infer on CloudMatrix 384 | Decode  | 1,943                      | 1.29 (INT8)                | 超大规模专家并行 (EP320), 融合通信算子, UB 总线 |
| SGLang on H100 (公开数据)            | Prefill | 7,417                      | 3.75 (FP8)                 | 专家并行负载均衡 (EPLB), RDMA                   |
| SGLang on H100 (公开数据)            | Decode  | 2,172                      | 1.10 (FP8)                 | 微批处理流水线, RDMA                            |
| SGLang on GB200 NVL72 (初步)         | Decode  | ~7,583                     | (待计算)                   | Blackwell DeepEP, 纯 NVLink 通信, DeepGEMM      |
