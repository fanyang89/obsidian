```
ntp-main. core.3710.1656747657  ntp-main. core.3736.1656835356  system-manager. core.13341.1656659537  zbs-ntpd

>>> datetime.fromtimestamp(1656747657)
datetime.datetime(2022, 7, 2, 15, 40, 57)

>>> datetime.fromtimestamp(1656835356)
datetime.datetime(2022, 7, 3, 16, 2, 36)

>>> datetime.fromtimestamp(1656659537)
datetime.datetime(2022, 7, 1, 15, 12, 17)
```

The *pthread_mutex_lock*(), *pthread_mutex_trylock*(), and *pthread_mutex_unlock*() functions may fail if:
**EINVAL** The value specified by *mutex* does not refer to an initialized mutex object.

```
   0x000055c200540ac7 <+71>:    lea    -0x18(%rax),%rdi
   0x000055c200540acb <+75>:    cmp    0x574386(%rip),%rdi        # 0x55c200ab4e58
   0x000055c200540ad2 <+82>:    jne    0x55c200540c31 <zbs::consensus::ZooKeeper::ZkEventHandler(zbs::consensus::ZooKeeper::ZkWatchContext*)+433>
   0x000055c200540ad8 <+88>:    mov    $0x20,%esi
   0x000055c200540add <+93>:    mov    %rbx,%rdi
   0x000055c200540ae0 <+96>:    callq  0x55c2007bbfe0 <tc_free_sized(void*, size_t)>
   0x000055c200540ae5 <+101>:   mov    %r12,%rdi
   0x000055c200540ae8 <+104>:   callq  0x55c2005317e0 <zbs::Mutex::Unlock()>
=> 0x000055c200540aed <+109>:   mov    -0x38(%rbp),%rax
   0x000055c200540af1 <+113>:   xor    %fs:0x28,%rax
   0x000055c200540afa <+122>:   jne    0x55c200540fbd <zbs::consensus::ZooKeeper::ZkEventHandler(zbs::consensus::ZooKeeper::ZkWatchContext*)+1341>
   0x000055c200540b00 <+128>:   add    $0x68,%rsp
   0x000055c200540b04 <+132>:   pop    %rbx
   0x000055c200540b05 <+133>:   pop    %r12
   0x000055c200540b07 <+135>:   pop    %r13
   0x000055c200540b09 <+137>:   pop    %r14
   0x000055c200540b0b <+139>:   pop    %r15
   0x000055c200540b0d <+141>:   pop    %rbp
   0x000055c200540b0e <+142>:   retq
   0x000055c200540b0f <+143>:   nop
   0x000055c200540b10 <+144>:   mov    %r12,%rdi
   0x000055c200540b13 <+147>:   lea    0x130(%r15),%r12
   0x000055c200540b1a <+154>:   callq  0x55c2005317e0 <zbs::Mutex::Unlock()>
```

```
rax            0x0      0
rbx            0x55c202cb5060   94291758895200
rcx            0x7fe3df3431f7   140616679043575
rdx            0x6      6
rsi            0x6fef   28655
rdi            0xe7e    3710
rbp            0x55c202de7fb0   0x55c202de7fb0
rsp            0x55c202de7f20   0x55c202de7f20
r8             0x0      0
r9             0x55c202de7c10   94291760151568
r10            0x8      8
r11            0x206    518
r12            0x55c20277e038   94291753427000
r13            0x55c20053f0a0   94291717517472
r14            0x55c20053f0b0   94291717517488
r15            0x55c20277e030   94291753426992
rip            0x55c200540aed   0x55c200540aed <zbs::consensus::ZooKeeper::ZkEventHandler(zbs::consensus::ZooKeeper::ZkWatchContext*)+109>
eflags         0x206    [ PF IF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
```

```
$rpm -q zbs
zbs-5.1.2-rc14.0.release.git.g42733ba17.el7.SMTX.SERVER_SAN.x86_64
```
