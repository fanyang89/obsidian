#eventfd #Linux

在 Linux 系统中，eventfd 是一个用来通知事件的文件描述符，timerfd 是定时器事件的文件描述符
简而言之，就是 eventfd 用来触发事件通知，timerfd 用来触发将来的事件通知。

# 接口

创建一个eventfd对象，或者说打开一个eventfd的文件，类似普通文件的open操作。

```c
int eventfd(unsigned int initval, int flags);
```

**划重点：eventfd 是一个计数相关的fd**。计数不为零是有**可读事件**发生，`read` 之后计数会清零，`write` 则会递增计数器。
eventfd 也是如此，eventfd 实现了 read/write 的调用，在调用里面实现了一套计数器的逻辑。write 仅仅是加计数，read 是读计数，并且清零。
长什么样子呢？笔者找了个进程来观摩下。

```text
root@ubuntu:~# ll /proc/14168/fd
lrwx------ 1 root root 64 Jul 10 22:12 3 -> anon_inode:[eventfd]
```

在 Linux 的 `/proc` 下每个进程都会有个目录，目录名为进程 ID 号，在这个目录能看到使用的资源信息，其中有个 fd 目录，就是进程打开的所有文件。看出猫腻了不？有个叫做 `[eventfd]` 的 fd 句柄。
