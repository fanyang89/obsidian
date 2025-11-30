```
fanyang@fanyangdeMacBook-Pro ~/Downloads/cluster-logs-2022093006-2022100812-03a2dc29 » ls -lah ./192_168_18_76_03a2dc29-6575-4a11-abab-2414297070d4/coredump_logs/coredump_logs

-rw-rw-rw-@  1 fanyang  staff   4.3M 10  7 14:33 urgent.core.6512.1665124376.gz    # Fri Oct  7 14:32:56z CST 2022
-rw-rw-rw-@  1 fanyang  staff   3.2M 10  7 12:01 urgent.core.9114.1665115258.gz    # Fri Oct  7 12:00:58 CST 2022
```

```
Thread 14 (Thread 0x7f476afdc700 (LWP 12364)):
#0  0x00007f47728789dd in fdatasync () from /lib64/libc.so.6
#1  0x000055a69908a851 in SyncFd (fd_path="/var/lib/zbs/metad/db//meta/zdb_config/006397.log", fd=<optimized out>) at leveldb/util/env_posix.cc:378
#2  leveldb::(anonymous namespace)::PosixWritableFile::Sync (this=0x55a69bd0c000) at leveldb/util/env_posix.cc:317
#3  0x000055a699065414 in leveldb::DBImpl::Write (this=0x55a69ba4b500, options=..., updates=<optimized out>) at leveldb/db/db_impl.cc:1260
#4  0x000055a69906127b in leveldb::DB::Put (this=this@entry=0x55a69ba4b500, opt=..., key=..., value=...) at leveldb/db/db_impl.cc:1532
#5  0x000055a6990612b9 in leveldb::DBImpl::Put (this=this@entry=0x55a69ba4b500, o=..., key=..., val=...) at leveldb/db/db_impl.cc:1220
#6  0x000055a698c0c3d8 in zbs::db::LevelDbBackend<std::string>::Put (this=this@entry=0x55a69bcf5e00, key="op_seq", value=..., sync=sync@entry=true) at src/db/leveldb_backend.h:330
#7  0x000055a698d99c46 in zbs::consensus::DBCluster::Sync (this=this@entry=0x55a69ba4bc00, op_seq=2268834) at src/consensus/db_cluster.cc:1259
#8  0x000055a698da517f in zbs::consensus::DBCluster::ApplyJournal (this=this@entry=0x55a69ba4bc00, ops=..., sync=sync@entry=true, cache=cache@entry=0x0, in_replay=in_replay@entry=false) at src/consensus/db_cluster.cc:1232
#9  0x000055a698da719d in zbs::consensus::DBCluster::CommitJournal(zbs::consensus::DbOps*, zbs::RpcStatus*, std::function<void ()> const&) (this=this@entry=0x55a69ba4bc00, db_ops=0x55a6d3251910, status=status@entry=0x55a6d3251820, cb=...) at src/consensus/db_cluster.cc:1116
#10 0x000055a698da76d0 in zbs::consensus::DBCluster::Commit(zbs::consensus::DbOps*, zbs::RpcStatus*, std::function<void ()> const&) (this=this@entry=0x55a69ba4bc00, ops=ops@entry=0x55a6d3251910, status=0x55a6d3251820, cb=...) at src/consensus/db_cluster.cc:1006
#11 0x000055a698da7a69 in zbs::consensus::DBCluster::HandleOp (this=0x55a69ba4bc00, ctx=0x55a6d3251860) at src/consensus/db_cluster.cc:464
#12 0x000055a698df3f39 in run_and_reset (this=0x55a6d0bab150) at src/common/coroutine.h:312
#13 zbs::Coroutine::LoopMain (this=0x55a6d0bab040) at src/common/coroutine.cc:615
#14 0x000055a698df3fc2 in zbs::Coroutine::s_co_main (lo=<optimized out>, hi=<optimized out>) at src/common/coroutine.cc:602

Thread 1 (Thread 0x7f476bfde700 (LWP 12363)):
#0  0x00007f47727be1f7 in raise () from /lib64/libc.so.6
#1  0x00007f47727bf8e8 in abort () from /lib64/libc.so.6
#2  0x000055a698cc4594 in operator() (__closure=0x55a69b137048) at src/meta/meta_server.cc:454
#3  std::_Function_handler<void(), zbs::meta::MetaServer::StartWatchDog()::<lambda()> >::_M_invoke(const std::_Any_data &) (__functor=...) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:316
#4  0x000055a698e24017 in operator() (this=0x55a69b137048) at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#5  zbs::Thread::DoStart (this=this@entry=0x55a69b136fc0) at src/common/thread.cc:242
#6  0x000055a698e24141 in zbs::Thread::StartEntry (obj=0x55a69b136fc0) at src/common/thread.cc:224
#7  0x00007f4775f73e25 in start_thread () from /lib64/libpthread.so.0
#8  0x00007f477288134d in clone () from /lib64/libc.so.6
```
