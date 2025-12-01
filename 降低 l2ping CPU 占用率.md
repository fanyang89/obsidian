## 2025-08-22

![[Pasted image 20250822182209.png]]

![[Pasted image 20250822182218.png]]

![[Pasted image 20250822182226.png]]

```
已完成的优化：

1. 复用 DecodingLayerParser（最大优化）

- 在 L2Pinger struct 中添加了 layer parser 相关字段
- 添加 initLayerParser() 初始化函数
- 避免每次解析都创建新的 parser

2. 对象池优化

- 添加 layerTypePool 对象池
- 在 parseL2PingMsg 中复用 foundLayerTypes 切片
- 减少内存分配和 GC 压力

3. 零拷贝优化

- 在 StartRecv 中启用 ZeroCopyEnabled = true
- 减少 packet 数据的内存拷贝

4. 线程安全

- 添加 parserMutex 保护 layer parser 的并发访问
- 确保在多 goroutine 环境下的安全性

预期性能提升：

- 内存分配减少 80-90%（通过复用 parser 和对象池）
- CPU 开销降低 30-50%（通过零拷贝和减少 allocations）
- GC 压力显著降低（通过对象池减少频繁的内存分配）

这些优化主要针对 parseL2PingMsg 函数，这是在 pprof 中显示为最慢的函数，通过减少内存分配和拷贝来大幅降低 CPU 开销。
```
