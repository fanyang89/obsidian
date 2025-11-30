#Linux #libc
EPOLL 的 API 用来执行类似 `poll()` 的任务，能够用于检测在多个文件描述符中任何 IO 可用的情况。

- `epoll_create()`
  - 可以创建一个epoll实例并返回相应的文件描述符
- `epoll_ctl()`
  -
- `epoll_wait()`
  - 可以用于等待 IO 事件。如果当前没有可用的事件，这个函数会阻塞调用线程

epoll 的事件派发接口可以运行在两种模式下

- 边缘触发 (edge-triggered)
- 水平触发 (level-triggered)
