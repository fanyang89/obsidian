# void_completion_t

```c
typedef void (*void_completion_t)(int rc, const void *data);
```

## rc

Connection loss/timeout triggers the completion with one of the following error codes:

- ZCONNECTIONLOSS -- lost connection to the server
- ZOPERATIONTIMEOUT -- connection timed out
- Zero indicates call was successful

## data

The pointer that was passed by the caller when the function that this completion corresponds to was invoked. The programmer is responsible for any memory freeing associated with the `data` pointer.

# zoo_adelete

```c
ZOOAPI int zoo_adelete(zhandle_t *zh, const char *path, int version,
        void_completion_t completion, const void *data);
```

# 流程

- 调用 `zoo_adelete(xxx, completion=cb)`
  - 构造请求 req buf
  - completion 塞 list
  - buf 塞 to_send queue
  - 关闭 oa，但是不 free req buf
  - 发送
    - 如果 request close，则 flush_send_queue；否则 wake io thread

线程：

- io thread：do_io
  - close_requested 为 false，则一直运行
  - read/write socket，塞进 to_process 队列
  - ## poll to_process 队列：
- completion thread：do_completion

# 什么情况下 completion 不会被调用？

当断开网络时，quorum cluster session expired -> `StopService()` -> `zookeeper_close()`
此时，do_completion 线程自然退出，无法继续处理 completion
db cluster 从 quorum cluster 中获取 zk，此时 completion 不会被调用，导致 dbcluster commit journal 一直在 yield。

# shared

修改前：

- zk::ctor
- zk::init
  - zookeeper_init(this)
- zk::dtor
  - zookeeper_close
- io 线程退出

修改后：

- zk::ctor
- zk::init
  - zookeeper_init(unique_ptr<shared>)
- zk::close
  - zookeeper_close
- io 线程退出
- zk::dtor
