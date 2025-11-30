#ZooKeeper #C
```c++
(gdb) thread 5
[Switching to thread 5 (LWP 2580383)]
#0  0x0000fffc6348e5a0 in pthread_cond_wait () from /lib64/libpthread.so.0
(gdb) bt
#0  0x0000fffc6348e5a0 in pthread_cond_wait () from /lib64/libpthread.so.0
#1  0x0000aaadfc213c30 in wait_sync_completion ()
#2  0x0000aaadfc211490 in zoo_wget_children_ ()
#3  0x0000aaadfbee61a8 in zbs::consensus::ZooKeeper::GetChildren (this=0xaaae398a8118, this@entry=0xaaae397f6be0, path="/zos/service/taskd",
    children=0xaaae39fdfc08, children@entry=0xaaae39fdfca8, watch=watch@entry=true) at src/consensus/zookeeper.cc:519
#4  0x0000aaadfbed9354 in zbs::consensus::QuorumCluster::GetAliveMembersFromZk (zk=0xaaae397f6be0, zk@entry=0xaaae398a8118, service_name="taskd",
    members=0xaaae39fdfd80, members@entry=0xaaae39fdfde0, watch=watch@entry=true) at src/consensus/quorum_cluster.cc:152
#5  0x0000aaadfbed9c30 in zbs::consensus::QuorumCluster::Join (this=this@entry=0xaaae398a8030, delete_exist=false) at src/consensus/quorum_cluster.cc:594
#6  0x0000aaadfbeda25c in zbs::consensus::QuorumCluster::CheckZK (this=0xaaae398a8030) at src/consensus/quorum_cluster.cc:578
#7  0x0000aaadfc115e80 in std::function<void ()>::operator()() const (this=0xaaae398b28d8) at /usr/include/c++/7.3.0/bits/std_function.h:706
#8  zbs::EventLoop::TimerCallback(int, std::function<void ()> const&, unsigned int, bool) (this=0xaaae398a8038, tfd=15, cb=..., mask=<optimized out>,
    del=false) at src/common/event_loop.cc:496
#9  0x0000aaadfc11686c in std::function<void (unsigned int)>::operator()(unsigned int) const (__args#0=<optimized out>, this=<optimized out>)
    at /usr/include/c++/7.3.0/bits/std_function.h:706
#10 zbs::EventLoop::ExecEvent (event=<optimized out>, this=<optimized out>) at src/common/event_loop.cc:290
#11 zbs::EventLoop::<lambda()>::operator() (__closure=<optimized out>) at src/common/event_loop.cc:318
#12 zbs::CoFunc::Impl<zbs::EventLoop::HandleSystemEvents(epoll_event*, int)::<lambda()> >::s_run_and_reset(zbs::CoFunc::storage_t &) (storage=...)
    at src/common/coroutine.h:276
#13 0x0000aaadfc10ed7c in zbs::CoFunc::run_and_reset (this=0xaaae397f0cc0) at src/common/coroutine.h:312
#14 zbs::Coroutine::LoopMain (this=0xaaae397f0b40) at src/common/coroutine.cc:598
#15 0x0000aaadfc10ede4 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585
```
