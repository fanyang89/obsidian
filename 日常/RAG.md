```
-> % go run ./cmd/ragctl compute
⠼ Computing embeddings (82181/-, 26 it/s) [52m20s]
```

- zbs 能支持没有机架的机箱么
- 虚拟机允许直接建虚拟网卡在存储网络上吗？

> Also, note that - `vllm:gpu_prefix_cache_queries` and `vllm:gpu_prefix_cache_hits` (Counters) replaces `vllm:gpu_prefix_cache_hit_rate` (Gauge).

> 缓存感知的调度策略。我们将缓存命中率定义为已缓存提示 token 数除以提示 token 数（number of cached prompt tokens / number of prompt tokens）。当等待队列中有许多请求时，执行请求的顺序会显著影响缓存命中率。例如，如果请求调度程序频繁地在不同的、不相关的请求之间进行切换，则会导致高速缓存抖动和较低的命中率。因此，RadixAttention 设计了一个具有缓存感知的调度算法来提高缓存命中率。这种算法会按匹配的前缀长度对请求进行排序，并优先处理具有较长匹配前缀的请求，而不是使用先到先服务的调度。

```
/usr/local/bin/ollama runner --ollama-engine --model /usr/share/ollama/.ollama/models/blobs/sha256-aeda25e63ebd698fab8638ffb778e68bed908b960d39d0becc650fa981609d25 --ctx-size 8192 --batch-size 512 --n-gpu-layers 35 --threads 16 --parallel 2 --port 44175
```
