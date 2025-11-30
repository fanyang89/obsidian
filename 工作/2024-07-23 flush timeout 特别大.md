```
(gdb) bt
#0  0x00007fdff6310cf2 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
#1  0x0000556733c3b236 in __gthread_cond_timedwait (__abs_timeout=0x7fdfebd571b0, __mutex=<optimized out>, __cond=0x5567368f41a8)
    at /opt/rh/devtoolset-7/root/usr/include/c++/7/x86_64-redhat-linux/bits/gthr-default.h:871
#2  std::condition_variable::__wait_until_impl<std::chrono::duration<long, std::ratio<1l, 1000000000l> > > (__atime=..., __lock=..., this=0x5567368f41a8)
    at /opt/rh/devtoolset-7/root/usr/include/c++/7/condition_variable:166
#3  std::condition_variable::wait_until<std::chrono::duration<long, std::ratio<1l, 1000000000l> > > (__atime=..., __lock=..., this=0x5567368f41a8)
    at /opt/rh/devtoolset-7/root/usr/include/c++/7/condition_variable:106
#4  std::condition_variable::wait_for<long, std::ratio<1l, 1l> > (__rtime=..., __lock=..., this=0x5567368f41a8)
    at /opt/rh/devtoolset-7/root/usr/include/c++/7/condition_variable:138

#5  google::(anonymous namespace)::LogFileObject::NotifyStop (timeout=915357848, this=0x5567368f4000) at glog/src/logging.cc:1257    < -- timeout 变成了一个特别大的值，之前是 5
#6  google::(anonymous namespace)::LogFileObject::FlushLogAndStop (this=0x5567368f4000, timeout=timeout@entry=5) at glog/src/logging.cc:1266
#7  0x0000556733c3b941 in google::(anonymous namespace)::LogFileObject::FlushLogAndStop (timeout=5, this=<optimized out>) at glog/src/logging.cc:1265
#8  google::LogDestination::FlushAll (timeout=5) at glog/src/logging.cc:914

#9  0x0000556733c3b982 in google::LogMessage::Fail () at glog/src/logging.cc:1797
#10 0x0000556733c3bc34 in google::LogMessage::SendToLog (this=0x7fdfebd57370) at glog/src/logging.cc:1751
#11 0x0000556733c352bd in google::LogMessage::Flush (this=0x7fdfebd57370) at glog/src/logging.cc:1620
#12 0x0000556733c3c539 in google::LogMessageFatal::~LogMessageFatal (this=<optimized out>, __in_chrg=<optimized out>) at glog/src/logging.cc:2337
#13 0x000055673396216c in zbs::Mutex::Lock (this=this@entry=0x556777558020) at src/common/locks.cc:32
#14 0x000055673394ab0d in zbs::GenericLockGuard<zbs::Mutex>::GenericLockGuard (m=0x556777558020, this=<synthetic pointer>) at src/common/locks.h:54
#15 zbs::AsyncServer::Shutdown (this=0x556777558000) at src/common/async_service.cc:79
#16 0x00005567336b5726 in zbs::meta::AccessManager::UrgentShutdown (this=<optimized out>) at src/meta/access_manager.cc:321
#17 0x000055673381d3d1 in zbs::meta::MetaServer::UrgentShutdown (this=0x556736952000) at src/meta/meta_server.cc:535
#18 0x000055673381d44f in zbs::meta::MetaServer::<lambda()>::operator() (__closure=0x55673690af88) at src/meta/meta_server.cc:433
#19 std::_Function_handler<void(), zbs::meta::MetaServer::StartWatchDog()::<lambda()> >::_M_invoke(const std::_Any_data &) (__functor=...)
    at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:316
#20 0x00005567339839b7 in std::function<void ()>::operator()() const (this=0x55673690af88)
    at /opt/rh/devtoolset-7/root/usr/include/c++/7/bits/std_function.h:706
#21 zbs::Thread::DoStart (this=this@entry=0x55673690af00) at src/common/thread.cc:233
#22 0x0000556733983ae1 in zbs::Thread::StartEntry (obj=0x55673690af00) at src/common/thread.cc:215
#23 0x00007fdff630ce25 in start_thread () from /lib64/libpthread.so.0
#24 0x00007fdff2bf534d in clone () from /lib64/libc.so.6
```

```
(gdb) f 6
#6  google::(anonymous namespace)::LogFileObject::FlushLogAndStop (this=0x5567368f4000, timeout=timeout@entry=5) at glog/src/logging.cc:1266
1266    in glog/src/logging.cc
(gdb) p timeout
$20 = 5
(gdb) p timeout@entry
$21 = 5
```

![[Pasted image 20240723143702.png|500]]

[[x86_64 fastcall convention]]

```
void LogDestination::FlushAll(int timeout) {
    if (!FLAGS_glog_async) {
        return;
    }
    for (int i = 0; i < NUM_SEVERITIES; ++i) {
      LogDestination::log_destinations_[i]->fileobject_.FlushLogAndStop(timeout);
    }
  }

(gdb)
Dump of assembler code for function _ZN6google14LogDestination8FlushAllEi:
   0x0000556733c3b910 <+0>:     lea    0x51f095(%rip),%rax        # 0x55673415a9ac <fLB::FLAGS_glog_async>
   0x0000556733c3b917 <+7>:     cmpb   $0x0,(%rax)
   0x0000556733c3b91a <+10>:    je     0x556733c3b950 <google::LogDestination::FlushAll(int)+64>
   0x0000556733c3b91c <+12>:    push   %r12
   0x0000556733c3b91e <+14>:    push   %rbp
   0x0000556733c3b91f <+15>:    mov    %edi,%ebp
   0x0000556733c3b921 <+17>:    push   %rbx
   0x0000556733c3b922 <+18>:    lea    0x51efb7(%rip),%rbx        # 0x55673415a8e0 <google::LogDestination::log_destinations_>
   0x0000556733c3b929 <+25>:    lea    0x20(%rbx),%r12
   0x0000556733c3b92d <+29>:    mov    (%rbx),%rdi
   0x0000556733c3b930 <+32>:    cmpq   $0x0,0x1a0(%rdi)
   0x0000556733c3b938 <+40>:    je     0x556733c3b941 <google::LogDestination::FlushAll(int)+49>
   0x0000556733c3b93a <+42>:    mov    %ebp,%esi
   0x0000556733c3b93c <+44>:    call   0x556733c3b160 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)>
=> 0x0000556733c3b941 <+49>:    add    $0x8,%rbx
   0x0000556733c3b945 <+53>:    cmp    %r12,%rbx
   0x0000556733c3b948 <+56>:    jne    0x556733c3b92d <google::LogDestination::FlushAll(int)+29>
   0x0000556733c3b94a <+58>:    pop    %rbx
   0x0000556733c3b94b <+59>:    pop    %rbp
   0x0000556733c3b94c <+60>:    pop    %r12
   0x0000556733c3b94e <+62>:    ret
   0x0000556733c3b94f <+63>:    nop
   0x0000556733c3b950 <+64>:    ret

(gdb) info registers
rax            0xfffffffffffffdfc  -516
rbx            0x55673415a8e0      93901743827168
rcx            0x7fdff6310cf2      140599884844274
rdx            0x2                 2
rsi            0x189               393
rdi            0x5567368f41ac      93901785350572
rbp            0x5                 0x5
rsp            0x7fdfebd571f0      0x7fdfebd571f0
r8             0x5567368f41d8      93901785350616
r9             0xffffffff          4294967295
r10            0x7fdfebd571b0      140599711068592
r11            0x206               518
r12            0x55673415a900      93901743827200
r13            0x556734162284      93901743858308
r14            0x86                134
r15            0x55673415a8e0      93901743827168
rip            0x556733c3b941      0x556733c3b941 <google::LogDestination::FlushAll(int)+49>
eflags         0x206               [ PF IF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
k0             0x0                 0
k1             0x0                 0
k2             0x0                 0
k3             0x0                 0
k4             0x0                 0
k5             0x0                 0
k6             0x0                 0
k7             0x0                 0
```

```
(gdb) info registers
rax            0xfffffffffffffdfc  -516
rbx            0x5567368f4000      93901785350144    // this
rcx            0x7fdff6310cf2      140599884844274
rdx            0x2                 2
rsi            0x189               393
rdi            0x5567368f41ac      93901785350572
rbp            0x5567368f4098      0x5567368f4098
rsp            0x7fdfebd571a0      0x7fdfebd571a0
r8             0x5567368f41d8      93901785350616
r9             0xffffffff          4294967295
r10            0x7fdfebd571b0      140599711068592
r11            0x206               518
r12            0x7fdff63116e0      140599884846816
r13            0x5567368f4090      93901785350288
r14            0x17e39fbaf6e0d493  1721395107940324499
r15            0x55673415a8e0      93901743827168
rip            0x556733c3b236      0x556733c3b236 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+214>
eflags         0x206               [ PF IF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
k0             0x0                 0
k1             0x0                 0
k2             0x0                 0
k3             0x0                 0
k4             0x0                 0
k5             0x0                 0
k6             0x0                 0
k7             0x0                 0

(gdb) info frame
Stack level 6, frame at 0x7fdfebd571f0:
 rip = 0x556733c3b236 in google::(anonymous namespace)::LogFileObject::FlushLogAndStop (glog/src/logging.cc:1266); saved rip = 0x556733c3b941
 called by frame at 0x7fdfebd57210, caller of frame at 0x7fdfebd571f0
 source language c++.
 Arglist at 0x7fdfebd57198, args: this=0x5567368f4000, timeout=timeout@entry=5
 Locals at 0x7fdfebd57198, Previous frame's sp is 0x7fdfebd571f0
 Saved registers:
  rbx at 0x7fdfebd571c0, rbp at 0x7fdfebd571c8, r12 at 0x7fdfebd571d0, r13 at 0x7fdfebd571d8, r14 at 0x7fdfebd571e0, rip at 0x7fdfebd571e8

(gdb) disassemble
Dump of assembler code for function google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int):
   0x0000556733c3b160 <+0>:     push   %r14
   0x0000556733c3b162 <+2>:     movslq %esi,%r14
   0x0000556733c3b165 <+5>:     push   %r13
   0x0000556733c3b167 <+7>:     push   %r12
   0x0000556733c3b169 <+9>:     push   %rbp
   0x0000556733c3b16a <+10>:    push   %rbx
   0x0000556733c3b16b <+11>:    mov    %rdi,%rbx
   0x0000556733c3b16e <+14>:    add    $0x1d8,%rdi
   0x0000556733c3b175 <+21>:    sub    $0x20,%rsp
   0x0000556733c3b179 <+25>:    mov    0x4cdda8(%rip),%r12        # 0x556734108f28
   0x0000556733c3b180 <+32>:    mov    %rdi,(%rsp)
   0x0000556733c3b184 <+36>:    movb   $0x0,0x8(%rsp)
   0x0000556733c3b189 <+41>:    test   %r12,%r12
   0x0000556733c3b18c <+44>:    je     0x556733c3b2a0 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+320>
   0x0000556733c3b192 <+50>:    call   0x556733684a00 <pthread_mutex_lock@plt>
   0x0000556733c3b197 <+55>:    test   %eax,%eax
   0x0000556733c3b199 <+57>:    jne    0x556733c3b2e5 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+389>
   0x0000556733c3b19f <+63>:    lea    0x98(%rbx),%rbp
   0x0000556733c3b1a6 <+70>:    movb   $0x1,0x8(%rsp)
   0x0000556733c3b1ab <+75>:    lea    0x90(%rbx),%r13
   0x0000556733c3b1b2 <+82>:    mov    %rbp,%rdi
   0x0000556733c3b1b5 <+85>:    call   0x556733684a00 <pthread_mutex_lock@plt>
   0x0000556733c3b1ba <+90>:    test   %eax,%eax
   0x0000556733c3b1bc <+92>:    jne    0x556733c3b2ec <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+396>
   0x0000556733c3b1c2 <+98>:    movb   $0x1,0x198(%rbx)
   0x0000556733c3b1c9 <+105>:   lea    0xc0(%rbx),%rdi
   0x0000556733c3b1d0 <+112>:   call   0x5567336850c0 <std::condition_variable::notify_one()@plt>
   0x0000556733c3b1d5 <+117>:   mov    %rbp,%rdi
   0x0000556733c3b1d8 <+120>:   call   0x556733684930 <pthread_mutex_unlock@plt>
   0x0000556733c3b1dd <+125>:   call   0x556733685380 <std::chrono::_V2::system_clock::now()@plt>
   0x0000556733c3b1e2 <+130>:   imul   $0x3b9aca00,%r14,%r14
   0x0000556733c3b1e9 <+137>:   mov    (%rsp),%rsi
   0x0000556733c3b1ed <+141>:   movabs $0x112e0be826d694b3,%rdx
   0x0000556733c3b1f7 <+151>:   lea    0x1a8(%rbx),%rdi
   0x0000556733c3b1fe <+158>:   add    %rax,%r14
   0x0000556733c3b201 <+161>:   mov    %r14,%rax
   0x0000556733c3b204 <+164>:   imul   %rdx
   0x0000556733c3b207 <+167>:   mov    %r14,%rax
   0x0000556733c3b20a <+170>:   sar    $0x3f,%rax
   0x0000556733c3b20e <+174>:   sar    $0x1a,%rdx
   0x0000556733c3b212 <+178>:   sub    %rax,%rdx
   0x0000556733c3b215 <+181>:   mov    %r14,%rax
   0x0000556733c3b218 <+184>:   mov    %rdx,0x10(%rsp)
   0x0000556733c3b21d <+189>:   imul   $0x3b9aca00,%rdx,%rdx
   0x0000556733c3b224 <+196>:   sub    %rdx,%rax
   0x0000556733c3b227 <+199>:   lea    0x10(%rsp),%rdx
   0x0000556733c3b22c <+204>:   mov    %rax,0x18(%rsp)
   0x0000556733c3b231 <+209>:   call   0x5567336856b0 <pthread_cond_timedwait@plt>
=> 0x0000556733c3b236 <+214>:   call   0x556733685380 <std::chrono::_V2::system_clock::now()@plt>
   0x0000556733c3b23b <+219>:   cmp    %rax,%r14
   0x0000556733c3b23e <+222>:   jg     0x556733c3b24f <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+239>
   0x0000556733c3b240 <+224>:   movb   $0x1,0x88(%rbx)
   0x0000556733c3b247 <+231>:   mov    %r13,%rdi
   0x0000556733c3b24a <+234>:   call   0x556733c3aa40 <google::(anonymous namespace)::LogQueue::PrintAllLogsToStderr()>
   0x0000556733c3b24f <+239>:   cmpb   $0x0,0x8(%rsp)
   0x0000556733c3b254 <+244>:   jne    0x556733c3b2c8 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+360>
   0x0000556733c3b256 <+246>:   mov    0x1a0(%rbx),%rdi
   0x0000556733c3b25d <+253>:   call   0x556733684a10 <std::thread::join()@plt>
   0x0000556733c3b262 <+258>:   mov    0x1a0(%rbx),%rdi
   0x0000556733c3b269 <+265>:   movq   $0x0,0x1a0(%rbx)
   0x0000556733c3b274 <+276>:   test   %rdi,%rdi
   0x0000556733c3b277 <+279>:   je     0x556733c3b289 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+297>
   0x0000556733c3b279 <+281>:   cmpq   $0x0,(%rdi)
   0x0000556733c3b27d <+285>:   jne    0x556733c3b2e0 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+384>
   0x0000556733c3b27f <+287>:   mov    $0x8,%esi
   0x0000556733c3b284 <+292>:   call   0x556733d4c5d0 <tc_free_sized(void*, size_t)>
   0x0000556733c3b289 <+297>:   add    $0x20,%rsp
   0x0000556733c3b28d <+301>:   pop    %rbx
   0x0000556733c3b28e <+302>:   pop    %rbp
   0x0000556733c3b28f <+303>:   pop    %r12
   0x0000556733c3b291 <+305>:   pop    %r13
   0x0000556733c3b293 <+307>:   pop    %r14
   0x0000556733c3b295 <+309>:   ret
   0x0000556733c3b296 <+310>:   nopw   %cs:0x0(%rax,%rax,1)
   0x0000556733c3b2a0 <+320>:   movb   $0x1,0x198(%rbx)
   0x0000556733c3b2a7 <+327>:   lea    0xc0(%rbx),%rdi
   0x0000556733c3b2ae <+334>:   lea    0x90(%rbx),%r13
   0x0000556733c3b2b5 <+341>:   movb   $0x1,0x8(%rsp)
   0x0000556733c3b2ba <+346>:   call   0x5567336850c0 <std::condition_variable::notify_one()@plt>
   0x0000556733c3b2bf <+351>:   jmp    0x556733c3b1dd <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+125>
   0x0000556733c3b2c4 <+356>:   nopl   0x0(%rax)
   0x0000556733c3b2c8 <+360>:   mov    (%rsp),%rdi
   0x0000556733c3b2cc <+364>:   test   %rdi,%rdi
   0x0000556733c3b2cf <+367>:   je     0x556733c3b256 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+246>
   0x0000556733c3b2d1 <+369>:   test   %r12,%r12
   0x0000556733c3b2d4 <+372>:   je     0x556733c3b256 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+246>
   0x0000556733c3b2d6 <+374>:   call   0x556733684930 <pthread_mutex_unlock@plt>
   0x0000556733c3b2db <+379>:   jmp    0x556733c3b256 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+246>
   0x0000556733c3b2e0 <+384>:   call   0x556733685ff0 <std::terminate()@plt>
   0x0000556733c3b2e5 <+389>:   mov    %eax,%edi
   0x0000556733c3b2e7 <+391>:   call   0x556733684cf0 <std::__throw_system_error(int)@plt>
   0x0000556733c3b2ec <+396>:   mov    %eax,%edi
   0x0000556733c3b2ee <+398>:   call   0x556733684cf0 <std::__throw_system_error(int)@plt>
   0x0000556733c3b2f3 <+403>:   cmpb   $0x0,0x8(%rsp)
   0x0000556733c3b2f8 <+408>:   mov    %rax,%rbx
   0x0000556733c3b2fb <+411>:   jne    0x556733c3b308 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+424>
   0x0000556733c3b2fd <+413>:   vzeroupper
   0x0000556733c3b300 <+416>:   mov    %rbx,%rdi
   0x0000556733c3b303 <+419>:   call   0x556733685260 <_Unwind_Resume@plt>
   0x0000556733c3b308 <+424>:   mov    %rsp,%rdi
   0x0000556733c3b30b <+427>:   vzeroupper
   0x0000556733c3b30e <+430>:   call   0x556733c3f0d0 <std::unique_lock<std::mutex>::unlock()>
   0x0000556733c3b313 <+435>:   jmp    0x556733c3b300 <google::(anonymous namespace)::LogFileObject::FlushLogAndStop(int)+416>
End of assembler dump.
```
