WatchDogThread.core.30018.1660368848

```
new Date(1660368848000)
2022-08-13T05:34:08.000Z

-rw-rw-rw-  1 root root 881M Aug 13 13:34 WatchDogThread.core.30018.1660368848
```

```
(gdb) info threads
  Id   Target Id         Frame
  17   Thread 0x7ff464f29700 (LWP 32889) 0x00007ff46e7c7a3d in poll () from /lib64/libc.so.6
  16   Thread 0x7ff46572a700 (LWP 32888) 0x00007ff4720de2ae in pthread_rwlock_wrlock () from /lib64/libpthread.so.0
  15   Thread 0x7ff463726700 (LWP 32892) 0x00007ff46e7d2923 in epoll_wait () from /lib64/libc.so.6
  14   Thread 0x7ff464728700 (LWP 32890) 0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
  13   Thread 0x7ff463f27700 (LWP 32891) 0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
  12   Thread 0x7ff468f31700 (LWP 30028) 0x00007ff46e7c7b4d in posix_fadvise64 () from /lib64/libc.so.6
  11   Thread 0x7ff46ab50700 (LWP 30024) 0x00007ff46e7d2923 in epoll_wait () from /lib64/libc.so.6
  10   Thread 0x7ff46672c700 (LWP 30033) 0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  9    Thread 0x7ff468730700 (LWP 30029) 0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  8    Thread 0x7ff469b4e700 (LWP 30026) 0x00007ff46e7d2923 in epoll_wait () from /lib64/libc.so.6
  7    Thread 0x7ff46a34f700 (LWP 30025) 0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  6    Thread 0x7ff46b351700 (LWP 30020) 0x00007ff4720e1b5d in recvmsg () from /lib64/libpthread.so.0
  5    Thread 0x7ff465f2b700 (LWP 32887) 0x00007ff4720de2ae in pthread_rwlock_wrlock () from /lib64/libpthread.so.0
  4    Thread 0x7ff466f2d700 (LWP 32878) 0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
  3    Thread 0x7ff472734080 (LWP 30018) 0x00007ff4720dbf57 in pthread_join () from /lib64/libpthread.so.0
  2    Thread 0x7ff467f2f700 (LWP 32877) 0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
* 1    Thread 0x7ff46772e700 (LWP 32876) 0x00007ff46e70f1f7 in raise () from /lib64/libc.so.6
```

```
thread 1
(gdb) bt
#0  0x00007ff46e70f1f7 in raise () from /lib64/libc.so.6
#1  0x00007ff46e7108e8 in abort () from /lib64/libc.so.6
#2  0x0000558f159e1a60 in operator() (__closure=<optimized out>) at src/meta/meta_server.cc:436
#3  std::_Function_handler<void(), zbs::meta::MetaServer::StartWatchDog()::<lambda()>::<lambda()> >::_M_invoke(const std::_Any_data &) (__functor=...) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:316
#4  0x0000558f15d11ab8 in operator() (this=0x558f17848c08) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#5  zbs::WatchDog::Watch (this=0x558f17848c00) at src/common/watchdog.cc:70
#6  0x0000558f15d0f4a7 in operator() (this=0x558f177462c8) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#7  zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x558f17843560, func=...) at src/common/timer_handle.cc:103
#8  0x0000558f15d0f5a9 in operator() (__closure=<optimized out>) at src/common/timer_handle.cc:42
#9  zbs::CoFunc::Impl<zbs::TimerHandle::Impl::Impl(zbs::CoroutineScheduler*, const func_t&, uint64_t, bool, std::string)::<lambda()> >::s_run_and_reset(zbs::CoFunc::storage_t &) (storage=...) at src/common/coroutine.h:276
#10 0x0000558f15cd9c89 in run_and_reset (this=0x558f177462b0) at src/common/coroutine.h:312
#11 zbs::Coroutine::LoopMain (this=0x558f177461a0) at src/common/coroutine.cc:598
#12 0x0000558f15cd9d12 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585
```

```
thread 2
(gdb) bt
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
#1  0x00007ff4720dcdcb in _L_lock_812 () from /lib64/libpthread.so.0
#2  0x00007ff4720dcc98 in pthread_mutex_lock () from /lib64/libpthread.so.0
#3  0x0000558f158e82d1 in zbs::Mutex::Lock (this=<optimized out>) at src/common/locks.h:23
#4  0x0000558f15ae59d6 in GenericLockGuard (m=0x558f17052108, this=<synthetic pointer>) at src/common/locks.h:58
#5  zbs::consensus::ZooKeeper::Close (this=this@entry=0x558f17052100) at src/consensus/zookeeper.cc:725
#6  0x0000558f159e2c30 in zbs::meta::MetaServer::UrgentShutdown (this=0x558f17052000) at src/meta/meta_server.cc:507
#7  0x0000558f159e2ccf in operator() (__closure=0x558f16fecd48) at src/meta/meta_server.cc:445
#8  std::_Function_handler<void(), zbs::meta::MetaServer::StartWatchDog()::<lambda()> >::_M_invoke(const std::_Any_data &) (__functor=...) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:316
#9  0x0000558f15d088c7 in operator() (this=0x558f16fecd48) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#10 zbs::Thread::DoStart (this=this@entry=0x558f16feccc0) at src/common/thread.cc:242
#11 0x0000558f15d089f1 in zbs::Thread::StartEntry (obj=0x558f16feccc0) at src/common/thread.cc:224
#12 0x00007ff4720dae25 in start_thread () from /lib64/libpthread.so.0
#13 0x00007ff46e7d234d in clone () from /lib64/libc.so.6
```

```
(gdb) thread 4
[Switching to thread 4 (Thread 0x7ff466f2d700 (LWP 32878))]
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
(gdb) bt
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
#1  0x00007ff4720dcdcb in _L_lock_812 () from /lib64/libpthread.so.0
#2  0x00007ff4720dcc98 in pthread_mutex_lock () from /lib64/libpthread.so.0
#3  0x0000558f158e82d1 in zbs::Mutex::Lock (this=<optimized out>) at src/common/locks.h:23
#4  0x0000558f15ae3878 in GenericLockGuard (m=0x558f17052108, this=<synthetic pointer>) at src/common/locks.h:58
#5  zbs::consensus::ZooKeeper::CreateAsync(std::string const&, std::string const&, int, std::function<void (zbs::Status, std::string, void*)> const&, void*) (this=this@entry=0x558f17052100, path="/zbs/meta/__mj_j/", '0' <repeats 12 times>, "117d/", '0' <repeats 12 times>, "119f",
    value="\356\a\360l\n\371\001\n\001a\020\001\032\022\070\066\060\065\066\070ce-6b00-4754\"\330\001\032$a98e30f8-1a63-11ed-846a-5254003a48ec*$f81e3017-c797-421b-b1ba-dcef3d8ad34a2\022Fc\000\300:\022b9ceee49-43be-4743R4\b\001\020\355\003\030\000 \000(/2\f\b\361\347ܗ\006\020\242ȟ\237\002:\f.\016\000\fB\v\b\366\005\034\030\264\336\364\065\262\006$F_\000t-9293-6e862ba0"..., flags=flags@entry=0, cb=..., data=0x0) at src/consensus/zookeeper.cc:349
#6  0x0000558f15ab25c0 in zbs::consensus::DBCluster::CommitJournal(zbs::consensus::DbOps*, zbs::RpcStatus*, std::function<void ()> const&) (this=this@entry=0x558f1767ec00, db_ops=0x558f4415d9e0, status=status@entry=0x558f4415d8c0, cb=...) at src/consensus/db_cluster.cc:1069
#7  0x0000558f15ab2d68 in zbs::consensus::DBCluster::Commit(zbs::consensus::DbOps*, zbs::RpcStatus*, std::function<void ()> const&) (this=this@entry=0x558f1767ec00, ops=ops@entry=0x558f4415d9e0, status=0x558f4415d8c0, cb=...) at src/consensus/db_cluster.cc:1004
#8  0x0000558f15ab30f9 in zbs::consensus::DBCluster::HandleOp (this=0x558f1767ec00, ctx=0x558f4415d900) at src/consensus/db_cluster.cc:472
#9  0x0000558f15cd9c89 in run_and_reset (this=0x558f17747e50) at src/common/coroutine.h:312
#10 zbs::Coroutine::LoopMain (this=0x558f17747d40) at src/common/coroutine.cc:598
#11 0x0000558f15cd9d12 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585
```

```
[Sat Aug 13 13:34:06 2022] sd 2:0:0:3: timing out command, waited 60s
[Sat Aug 13 13:34:06 2022] sd 2:0:0:3: [sdd] FAILED Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE
[Sat Aug 13 13:34:06 2022] sd 2:0:0:3: [sdd] Sense Key : Aborted Command [current]
[Sat Aug 13 13:34:06 2022] sd 2:0:0:3: [sdd] Add. Sense: I/O process terminated
[Sat Aug 13 13:34:06 2022] sd 2:0:0:3: [sdd] CDB: Write(10) 2a 00 09 c8 2c 08 00 02 00 00
[Sat Aug 13 13:34:06 2022] blk_update_request: I/O error, dev sdd, sector 164113416
[Sat Aug 13 13:34:07 2022] potentially unexpected fatal signal 6.
[Sat Aug 13 13:34:07 2022] CPU: 0 PID: 32876 Comm: WatchDogThread Kdump: loaded Not tainted 3.10.0-1062.1.2.el7.smartx.1.x86_64 #1
[Sat Aug 13 13:34:07 2022] Hardware name: SmartX SMTX OS, BIOS 1.10.2-3.el7 04/01/2014
[Sat Aug 13 13:34:07 2022] task: ffff906ffeaa1070 ti: ffff9070a8254000 task.ti: ffff9070a8254000
[Sat Aug 13 13:34:07 2022] RIP: 0033:[<00007ff46e70f1f7>]  [<00007ff46e70f1f7>] 0x7ff46e70f1f7
[Sat Aug 13 13:34:07 2022] RSP: 002b:0000558f17953db8  EFLAGS: 00000206
[Sat Aug 13 13:34:07 2022] RAX: 0000000000000000 RBX: 0000558f17848c00 RCX: ffffffffffffffff
[Sat Aug 13 13:34:07 2022] RDX: 0000000000000006 RSI: 000000000000806c RDI: 0000000000007542
[Sat Aug 13 13:34:07 2022] RBP: 0000558f17953ef0 R08: 0000000000000000 R09: 0000558f17953f40
[Sat Aug 13 13:34:07 2022] R10: 0000000000000008 R11: 0000000000000206 R12: 0000000000000000
[Sat Aug 13 13:34:07 2022] R13: 0000558f17953f00 R14: 00000000029337f9 R15: 0000558f17767cb0
[Sat Aug 13 13:34:07 2022] FS:  00007ff46772e700(0000) GS:ffff9070aba00000(0000) knlGS:0000000000000000
[Sat Aug 13 13:34:07 2022] CS:  0010 DS: 0000 ES: 0000 CR0: 0000000080050033
[Sat Aug 13 13:34:07 2022] CR2: 0000558f15d3fd90 CR3: 0000000352e10000 CR4: 00000000003606f0
[Sat Aug 13 13:34:07 2022] DR0: 0000000000000000 DR1: 0000000000000000 DR2: 0000000000000000
[Sat Aug 13 13:34:07 2022] DR3: 0000000000000000 DR6: 00000000fffe0ff0 DR7: 0000000000000400
```

```
(gdb) thread 5
[Switching to thread 5 (Thread 0x7ff465f2b700 (LWP 32887))]
#0  0x00007ff4720de2ae in pthread_rwlock_wrlock () from /lib64/libpthread.so.0
(gdb) bt
#0  0x00007ff4720de2ae in pthread_rwlock_wrlock () from /lib64/libpthread.so.0
#1  0x0000558f15d33f24 in Lock (this=0x558f16194de0 <google::log_mutex>) at glog/src/base/mutex.h:250
#2  MutexLock (mu=0x558f16194de0 <google::log_mutex>, this=<synthetic pointer>) at glog/src/base/mutex.h:290
#3  google::LogMessage::Flush (this=this@entry=0x558f447979a0) at glog/src/logging.cc:1310
#4  0x0000558f15d34029 in google::LogMessage::~LogMessage (this=0x558f447979a0, __in_chrg=<optimized out>) at glog/src/logging.cc:1270
#5  0x0000558f15903273 in zbs::meta::AccessManager::EnqueueRecoverCmd (this=0x558f177b8d00, recover_cmd=std::shared_ptr (count 1, weak 0) 0x558f17e5f570, alive_loc=<optimized out>) at src/meta/access_manager.cc:540
#6  0x0000558f15a4fd9b in zbs::meta::RecoverManager::AddRecoverCmdUnlock (this=0x558f177c9100, cmd=...) at src/meta/recover_manager.cc:370
#7  0x0000558f15a4def1 in zbs::meta::RecoverManager::GenerateRecoverCmds (this=this@entry=0x558f177c9100) at src/meta/recover_manager.cc:712
#8  0x0000558f15a580de in zbs::meta::RecoverManager::DoScan (this=0x558f177c9100) at src/meta/recover_manager.cc:255
#9  0x0000558f15d0f4a7 in operator() (this=0x558f17e2e7a8) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#10 zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x558f17843ce0, func=...) at src/common/timer_handle.cc:103
#11 0x0000558f15d0f5a9 in operator() (__closure=<optimized out>) at src/common/timer_handle.cc:42
#12 zbs::CoFunc::Impl<zbs::TimerHandle::Impl::Impl(zbs::CoroutineScheduler*, const func_t&, uint64_t, bool, std::string)::<lambda()> >::s_run_and_reset(zbs::CoFunc::storage_t &) (storage=...) at src/common/coroutine.h:276
#13 0x0000558f15cd9c89 in run_and_reset (this=0x558f17e2e790) at src/common/coroutine.h:312
#14 zbs::Coroutine::LoopMain (this=0x558f17e2e680) at src/common/coroutine.cc:598
#15 0x0000558f15cd9d12 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585
```

```
(gdb) thread 7
(gdb) bt
#0  0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
#1  0x0000558f15dadd13 in wait_sync_completion ()
#2  0x0000558f15dab600 in zoo_wget_children_ ()
#3  0x0000558f15ae4616 in zbs::consensus::ZooKeeper::GetChildren (this=this@entry=0x558f17052100, path="/zos/service/meta", children=children@entry=0x558f17e4fcd0, watch=watch@entry=true) at src/consensus/zookeeper.cc:519
#4  0x0000558f15ac1482 in zbs::consensus::QuorumCluster::GetAliveMembersFromZk (zk=zk@entry=0x558f17052100, service_name="meta", members=members@entry=0x558f17e4fdd0, watch=watch@entry=true) at src/consensus/quorum_cluster.cc:152
#5  0x0000558f15ac1cd2 in zbs::consensus::QuorumCluster::Join (this=this@entry=0x558f17052018, delete_exist=delete_exist@entry=false) at src/consensus/quorum_cluster.cc:594
#6  0x0000558f15ac2244 in zbs::consensus::QuorumCluster::CheckZK (this=0x558f17052018) at src/consensus/quorum_cluster.cc:578
#7  0x0000558f15cddfec in operator() (this=0x558f17672a68) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#8  zbs::EventLoop::TimerCallback(int, std::function<void ()> const&, unsigned int, bool) (this=0x558f17052020, tfd=22, cb=..., mask=<optimized out>, del=<optimized out>) at src/common/event_loop.cc:496
#9  0x0000558f15cde06a in operator() (__args#0=<optimized out>, this=0x558f171c05c0) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#10 zbs::EventLoop::ExecEvent (this=<optimized out>, event=0x558f171c05c0) at src/common/event_loop.cc:290
#11 0x0000558f15cd9c89 in run_and_reset (this=0x558f17e2e110) at src/common/coroutine.h:312
#12 zbs::Coroutine::LoopMain (this=0x558f17e2e000) at src/common/coroutine.cc:598
#13 0x0000558f15cd9d12 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585
```

```
thread 9
(gdb) bt
#0  0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
#1  0x0000558f15dae0eb in do_completion ()
#2  0x00007ff4720dae25 in start_thread () from /lib64/libpthread.so.0
#3  0x00007ff46e7d234d in clone () from /lib64/libc.so.6
```

```
(gdb) thread 10
[Switching to thread 10 (Thread 0x7ff46672c700 (LWP 30033))]
#0  0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
(gdb) bt
#0  0x00007ff4720de945 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
#1  0x00007ff46f066a6c in std::condition_variable::wait(std::unique_lock<std::mutex>&) () from /lib64/libstdc++.so.6
#2  0x0000558f15d7ebd8 in Wait (this=0x558f161960d0 <leveldb::Env::Default()::env_container+48>) at leveldb/port/port_stdcxx.h:77
#3  BackgroundThreadMain (this=0x558f161960a0 <leveldb::Env::Default()::env_container>) at leveldb/util/env_posix.cc:811
#4  leveldb::(anonymous namespace)::PosixEnv::BackgroundThreadEntryPoint (env=0x558f161960a0 <leveldb::Env::Default()::env_container>) at leveldb/util/env_posix.cc:726
#5  0x0000558f15ddfe5f in execute_native_thread_routine ()
#6  0x00007ff4720dae25 in start_thread () from /lib64/libpthread.so.0
#7  0x00007ff46e7d234d in clone () from /lib64/libc.so.6
```

```
(gdb) thread 12
[Switching to thread 12 (Thread 0x7ff468f31700 (LWP 30028))]
#0  0x00007ff46e7c7b4d in posix_fadvise64 () from /lib64/libc.so.6
(gdb) bt
#0  0x00007ff46e7c7b4d in posix_fadvise64 () from /lib64/libc.so.6
#1  0x0000558f15d3ac68 in google::(anonymous namespace)::LogFileObject::Write (this=0x558f16fe8630, force_flush=<optimized out>, timestamp=<optimized out>, message=<optimized out>, message_len=308) at glog/src/logging.cc:1123
#2  0x0000558f15d36b5e in MaybeLogToLogfile (len=308, message=0x558f1773e004 "E0813 13:24:16.625602 30028 main.cc:82] [ZOO] handle_socket_error_msg:2390 2022-08-13 13:24:16,625:30018(0x7ff468f31700):ZOO_ERROR@handle_socket_error_msg@2390: Socket [10.188.139.225:2181] zk retcode"..., timestamp=1660368256,
    severity=<optimized out>) at glog/src/logging.cc:764
#3  LogToAllLogfiles (len=308, message=0x558f1773e004 "E0813 13:24:16.625602 30028 main.cc:82] [ZOO] handle_socket_error_msg:2390 2022-08-13 13:24:16,625:30018(0x7ff468f31700):ZOO_ERROR@handle_socket_error_msg@2390: Socket [10.188.139.225:2181] zk retcode"..., timestamp=1660368256, severity=<optimized out>)
    at glog/src/logging.cc:776
#4  google::LogMessage::SendToLog (this=0x7ff468f2fce0) at glog/src/logging.cc:1388
#5  0x0000558f15d33ddd in google::LogMessage::Flush (this=this@entry=0x7ff468f2fce0) at glog/src/logging.cc:1311
#6  0x0000558f15d34029 in google::LogMessage::~LogMessage (this=0x7ff468f2fce0, __in_chrg=<optimized out>) at glog/src/logging.cc:1270
#7  0x0000558f15ceb647 in zbs::ZookeeperLogger (curLevel=<optimized out>, line=2390, funcName=<optimized out>,
    message=0x558f1772b000 "2022-08-13 13:24:16,625:30018(0x7ff468f31700):ZOO_ERROR@handle_socket_error_msg@2390: Socket [10.188.139.225:2181] zk retcode=-7, errno=110(Connection timed out): connection to 10.188.139.225:2181 tim"...) at src/common/main.cc:93
#8  0x0000558f15da3a89 in log_message ()
#9  0x0000558f15da7a4a in handle_socket_error_msg ()
#10 0x0000558f15da86a0 in zookeeper_interest ()
#11 0x0000558f15dadf22 in do_io ()
#12 0x00007ff4720dae25 in start_thread () from /lib64/libpthread.so.0
#13 0x00007ff46e7d234d in clone () from /lib64/libc.so.6
```

```
(gdb) thread 13
[Switching to thread 13 (Thread 0x7ff463f27700 (LWP 32891))]
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
(gdb) bt
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
#1  0x00007ff4720dcdcb in _L_lock_812 () from /lib64/libpthread.so.0
#2  0x00007ff4720dcc98 in pthread_mutex_lock () from /lib64/libpthread.so.0
#3  0x0000558f158e82d1 in zbs::Mutex::Lock (this=this@entry=0x558f177b8d10) at src/common/locks.h:23
#4  0x0000558f158fec1b in zbs::meta::AccessManager::ListISCSISession(unsigned __int128, std::unordered_map<unsigned char, zbs::meta::ISCSISession, std::hash<unsigned char>, std::equal_to<unsigned char>, std::allocator<std::pair<unsigned char const, zbs::meta::ISCSISession> > >*) (this=0x558f177b8d00,
    session_id=<optimized out>, sessions=sessions@entry=0x558f17767ef0) at src/meta/access_manager.cc:1697
#5  0x0000558f15976139 in zbs::meta::ISCSIServer::CheckAccessPoint (this=0x558f17d642c0) at src/meta/iscsi_server.cc:3607
#6  0x0000558f15d0f4a7 in operator() (this=0x558f17746128) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#7  zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x558f17843260, func=...) at src/common/timer_handle.cc:103
#8  0x0000558f15d0f5a9 in operator() (__closure=<optimized out>) at src/common/timer_handle.cc:42
#9  zbs::CoFunc::Impl<zbs::TimerHandle::Impl::Impl(zbs::CoroutineScheduler*, const func_t&, uint64_t, bool, std::string)::<lambda()> >::s_run_and_reset(zbs::CoFunc::storage_t &) (storage=...) at src/common/coroutine.h:276
#10 0x0000558f15cd9c89 in run_and_reset (this=0x558f17746110) at src/common/coroutine.h:312
#11 zbs::Coroutine::LoopMain (this=0x558f17746000) at src/common/coroutine.cc:598
#12 0x0000558f15cd9d12 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585
```

```
(gdb) thread 14
[Switching to thread 14 (Thread 0x7ff464728700 (LWP 32890))]
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
(gdb) bt
#0  0x00007ff4720e142d in __lll_lock_wait () from /lib64/libpthread.so.0
#1  0x00007ff4720dcdcb in _L_lock_812 () from /lib64/libpthread.so.0
#2  0x00007ff4720dcc98 in pthread_mutex_lock () from /lib64/libpthread.so.0
#3  0x0000558f158e82d1 in zbs::Mutex::Lock (this=this@entry=0x558f177b8d10) at src/common/locks.h:23
#4  0x0000558f158e9b57 in zbs::meta::AccessManager::SummaryChunksPerf (this=this@entry=0x558f177b8d00, summary=0x558f177a2ae0, chunks=std::vector of length 4, capacity 4 = {...}) at src/meta/access_manager.cc:1481
#5  0x0000558f15a5ac02 in zbs::meta::StatusServer::StatStoragePool (this=this@entry=0x558f448f4000, storage_pool=storage_pool@entry=0x558f448c33f0) at src/meta/status_server.cc:1013
#6  0x0000558f15a5ae80 in zbs::meta::StatusServer::StatStoragePools (this=this@entry=0x558f448f4000, response=response@entry=0x558f440bbdb0) at src/meta/status_server.cc:932
#7  0x0000558f15a6937a in zbs::meta::StatusServer::CollectMetrics (this=0x558f448f4000) at src/meta/status_server.cc:749
#8  0x0000558f15d0f4a7 in operator() (this=0x558f17d627a8) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#9  zbs::TimerHandle::Impl::Routine(std::function<void ()> const&) (this=0x558f17e5e300, func=...) at src/common/timer_handle.cc:103
#10 0x0000558f15d0f5a9 in operator() (__closure=<optimized out>) at src/common/timer_handle.cc:42
#11 zbs::CoFunc::Impl<zbs::TimerHandle::Impl::Impl(zbs::CoroutineScheduler*, const func_t&, uint64_t, bool, std::string)::<lambda()> >::s_run_and_reset(zbs::CoFunc::storage_t &) (storage=...) at src/common/coroutine.h:276
#12 0x0000558f15cd9c89 in run_and_reset (this=0x558f17d62790) at src/common/coroutine.h:312
#13 zbs::Coroutine::LoopMain (this=0x558f17d62680) at src/common/coroutine.cc:598
#14 0x0000558f15cd9d12 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:585

```

```
(gdb) thread 16
[Switching to thread 16 (Thread 0x7ff46572a700 (LWP 32888))]
#0  0x00007ff4720de2ae in pthread_rwlock_wrlock () from /lib64/libpthread.so.0
(gdb) bt
#0  0x00007ff4720de2ae in pthread_rwlock_wrlock () from /lib64/libpthread.so.0
#1  0x0000558f15d33f24 in Lock (this=0x558f16194de0 <google::log_mutex>) at glog/src/base/mutex.h:250
#2  MutexLock (mu=0x558f16194de0 <google::log_mutex>, this=<synthetic pointer>) at glog/src/base/mutex.h:290
#3  google::LogMessage::Flush (this=this@entry=0x7ff46570b350) at glog/src/logging.cc:1310
#4  0x0000558f15d34029 in google::LogMessage::~LogMessage (this=0x7ff46570b350, __in_chrg=<optimized out>) at glog/src/logging.cc:1270
#5  0x0000558f15ac837c in zbs::consensus::MasterSession::HandleExpire (this=this@entry=0x558f17739a50) at src/consensus/session.cc:196
#6  0x0000558f15ac8fe8 in zbs::consensus::MasterSession::OnTimerCallback (this=0x558f17739a50) at src/consensus/session.cc:179
#7  0x0000558f15cdc7e5 in operator() (this=0x7ff46570b430) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#8  Run (this=0x558f17739b08) at src/common/timer.h:52
#9  zbs::EventLoop::HandleTimerEvents (this=this@entry=0x558f177b8d38) at src/common/event_loop.cc:347
#10 0x0000558f15cde56a in zbs::EventLoop::Loop (this=this@entry=0x558f177b8d38, timeout_ms=timeout_ms@entry=10000, max_time=max_time@entry=0) at src/common/event_loop.cc:77
#11 0x0000558f158e7d59 in zbs::meta::AccessManager::Start (this=0x558f177b8d00) at src/meta/access_manager.cc:194
#12 0x0000558f15d088c7 in operator() (this=0x558f177b8f68) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#13 zbs::Thread::DoStart (this=this@entry=0x558f177b8ee0) at src/common/thread.cc:242
#14 0x0000558f15d089f1 in zbs::Thread::StartEntry (obj=0x558f177b8ee0) at src/common/thread.cc:224
#15 0x00007ff4720dae25 in start_thread () from /lib64/libpthread.so.0
#16 0x00007ff46e7d234d in clone () from /lib64/libc.so.6
```
