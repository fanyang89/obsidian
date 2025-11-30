#cpp

# TLS

Thread-local storage

- `pthread_key_create` 和 `pthread_key_delete` 创建 pthread_key_t
- `pthread_setspecific` 设置 TLS
- `pthread_setspecific` 获取 TLS

gcc/clang `__thread` 声明 TLS 变量
VC++ `__declspec(thread)`

从 C++ 11 开始，errno 被修改为一个 TLS Variable

`upper_bound()` 函数可以理解为：**大于目标元素的第一个数/位置**。
对于 `lower_bound()` 函数，找到的是 **大于等于** 目标元素的数值和下标。
