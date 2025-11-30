```
(gdb) co-list
No     Addr             Tid      State       Frame
0      0x61a000030680   1464774  YIELDING    0x0000555cef316077 in zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x60c000036040, func=...) at ../src/common/timer_handle.cc:108
1      0x61a000025280   1464772  YIELDING    0x0000555cef316077 in zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x60c000029080, func=...) at ../src/common/timer_handle.cc:108

# quorum
2      0x61a00001fe80   1464771  YIELDING    0x0000555cef191edb in zbs::CoMutex::Lock (this=0x6190000205d8) at ../src/common/co_locks.cc:21
3      0x61a000010280   1464771  YIELDING    0x0000555cef191edb in zbs::CoMutex::Lock (this=0x619000020478) at ../src/common/co_locks.cc:21
4      0x61a00000fc80   1464771  YIELDING    0x0000555cef1a81ca in zbs::CoSync::WaitAndReset (this=0x7fa3a53824f0) at ../src/common/coroutine_utils.h:24

5      0x61a000000c80   1464767  YIELDING    0x0000555cef316077 in zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x60c0000100c0, func=...) at ../src/common/timer_handle.cc:108


(gdb) info threads
  Id   Target Id                     Frame
* 1    LWP 1464764 "zbs-metad"       0x0000555cee55aed5 in swapcontext ()
  2    LWP 1464765 "zbs-metad-ust"   0x00007fa3a8690c59 in syscall () from /lib64/libc.so.6
  3    LWP 1464766 "zbs-metad-ust"   0x00007fa3a8690c59 in syscall () from /lib64/libc.so.6
  4    LWP 1464767 "meta-sm-server"  0x00007fa3a8696607 in epoll_wait () from /lib64/libc.so.6
  5    LWP 1464768 "meta-rpc-server" 0x00007fa3a8696607 in epoll_wait () from /lib64/libc.so.6
  6    LWP 1464769 "zoo-io"          0x00007fa3a868b809 in poll () from /lib64/libc.so.6
  7    LWP 1464770 "zoo-completion"  0x00007fa3a8c5ca0c in pthread_cond_wait () from /lib64/libpthread.so.0
  8    LWP 1464771 "quorum"          0x00007fa3a8696607 in epoll_wait () from /lib64/libc.so.6
  9    LWP 1464772 "watchdog"        0x00007fa3a8696607 in epoll_wait () from /lib64/libc.so.6
  10   LWP 1464773 "urgent"          0x00007fa3a8663bb0 in nanosleep () from /lib64/libc.so.6
  11   LWP 1464774 "db-cluster-io"   0x00007fa3a8696607 in epoll_wait () from /lib64/libc.so.6

(gdb) co-bt 0x61a00001fe80
coroutine's state is zbs::CoroutineState::YIELDING
coroutine last ran on thread 1464771
#0  0x0000555cee55aed5 in swapcontext ()
#1  0x0000555cef19c9ab in zbs::detail::CoroutineEnv::SwitchOut (this=0x61a00001fe80) at ../src/common/coroutine.cc:154
#2  0x0000555cef19ffbc in zbs::Coroutine::SchedTo (this=0x61a00001fe80, scheduler=0x0) at ../src/common/coroutine.cc:389
#3  0x0000555cef19fe09 in zbs::Coroutine::Yield (this=0x61a00001fe80) at ../src/common/coroutine.cc:382
#4  0x0000555cef191edb in zbs::CoMutex::Lock (this=0x6190000205d8) at ../src/common/co_locks.cc:21
#5  0x0000555cee7ad72d in zbs::GenericLockGuard<zbs::CoMutex>::GenericLockGuard (this=0x7fa3a121c020, m=0x6190000205d8) at ../src/common/locks.h:54
#6  0x0000555cefefbb43 in zbs::consensus::QuorumServer::OnStatusChange (this=0x619000020380, event=zbs::consensus::QuorumClusterEvent::STOPPED)
    at ../src/consensus/quorum_server.cc:70
#7  0x0000555cefefa580 in zbs::consensus::QuorumServer::<lambda(zbs::consensus::QuorumClusterEvent)>::operator()(zbs::consensus::QuorumClusterEvent) const
    (__closure=0x6190000205b0, e=zbs::consensus::QuorumClusterEvent::STOPPED) at ../src/consensus/quorum_server.cc:21

(gdb) co-bt 0x61a000010280
#4  0x0000555cef191edb in zbs::CoMutex::Lock (this=0x619000020478) at ../src/common/co_locks.cc:21
#5  0x0000555cee7ad72d in zbs::GenericLockGuard<zbs::CoMutex>::GenericLockGuard (this=0x7fa3a5360230, m=0x619000020478) at ../src/common/locks.h:54
#6  0x0000555cefee914a in zbs::consensus::QuorumCluster::Watcher (this=0x6190000203b8, type=4, state=3, path="/zos/service/meta")
    at ../src/consensus/quorum_cluster.cc:209

(gdb) co-bt 0x61a00000fc80
#4  0x0000555cef1a81ca in zbs::CoSync::WaitAndReset (this=0x7fa3a53824f0) at ../src/common/coroutine_utils.h:24
#5  0x0000555cef1a86e8 in zbs::CoSafeSync::WaitAndReset (this=0x7fa3a53824e0) at ../src/common/coroutine_utils.cc:19
#6  0x0000555ceebb159f in zbs::meta::MetaServer::StartService (this=0x619000020380) at ../src/meta/meta_server.cc:349
#7  0x0000555cefefbcef in zbs::consensus::QuorumServer::OnStatusChange (this=0x619000020380, event=zbs::consensus::QuorumClusterEvent::ENTER_RUNNING)
    at ../src/consensus/quorum_server.cc:76
#8  0x0000555cefefa580 in zbs::consensus::QuorumServer::<lambda(zbs::consensus::QuorumClusterEvent)>::operator()(zbs::consensus::QuorumClusterEvent) const
    (__closure=0x6190000205b0, e=zbs::consensus::QuorumClusterEvent::ENTER_RUNNING) at ../src/consensus/quorum_server.cc:21
```
