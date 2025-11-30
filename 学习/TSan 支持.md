Fiber 是一种轻量级的线程抽象，它们在用户空间中进行调度和上下文切换，而不是在内核中。与操作系统线程相比，fibers 可以在有限的资源下实现更高的并发性。然而，由于 fibers 由用户级库进行调度，而非内核，传统的线程分析工具（如 TSan）可能无法正确识别和处理由 fibers 导致的数据竞争问题。

为了支持 fibers，ThreadSanitizer 提供了一组附加的 API，允许用户显式地通知 TSan 在 fiber 之间进行上下文切换。这些 API 使得 TSan 可以正确地跟踪 fiber 的同步和并发行为，并报告潜在的数据竞争问题。

以下是 TSan 提供的一些主要 fiber 相关 API：

- `__tsan_create_fiber`：创建一个新的 fiber。
- `__tsan_destroy_fiber`：销毁一个 fiber。
- `__tsan_switch_to_fiber`：在当前 fiber 和目标 fiber 之间进行上下文切换。
- `__tsan_acquire` 和 `__tsan_release`：用于表示 fiber 间的同步操作。

[[工作/zbs-meta+tsan]]
