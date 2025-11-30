x86: Remove arch-specific low level lock implementation

This patch removes the arch-specific x86 assembly implementation for
low level locking and consolidate both 64 bits and 32 bits in a
single implementation.

Different than other architectures, x86 lll_trylock, lll_lock, and
lll_unlock implements a single-thread optimization to avoid atomic
operation, using cmpxchgl instead. This patch implements by using
the new single-thread.h definitions in a generic way, although using
the previous semantic.

The lll_cond_trylock, lll_cond_lock, and lll_timedlock just use
atomic operations plus calls to lll_lock_wait\*.

For **lll_lock_wait_private and **lll_lock_wait the generic implemtation
there is no indication that assembly implementation is required
performance-wise.

Checked on x86_64-linux-gnu and i686-linux-gnu.

    * sysdeps/nptl/lowlevellock.h (__lll_trylock): New macro.
    (lll_trylock): Call __lll_trylock.
    * sysdeps/unix/sysv/linux/i386/libc-lowlevellock.S: Remove file.
    * sysdeps/unix/sysv/linux/i386/lll_timedlock_wait.c: Likewise.
    * sysdeps/unix/sysv/linux/i386/lowlevellock.S: Likewise.
    * sysdeps/unix/sysv/linux/i386/lowlevellock.h: Likewise.
    * sysdeps/unix/sysv/linux/x86_64/libc-lowlevellock.S: Likewise.
    * sysdeps/unix/sysv/linux/x86_64/lll_timedlock_wait.c: Likewise.
    * sysdeps/unix/sysv/linux/x86_64/lowlevellock.S: Likewise.
    * sysdeps/unix/sysv/linux/x86_64/lowlevellock.h: Likewise.
    * sysdeps/unix/sysv/linux/x86/lowlevellock.h: New file.
    * sysdeps/unix/sysv/linux/x86_64/cancellation.S: Include
    lowlevellock-futex.h.

Remove x86_64 specific lowlevellock/cancellation

The x86_64 specific implemention has CFI directives like
`.cfi_adjust_cfa_offset 128` which are incorrect when RBP is used as the
canonical frame address.

This follows the spirit of the following two commits by removing the
x86_64 specific implementation. The generic implementation will be used.

- eb76e5b465a4b7b569cde4b4f57d1fcb4695c1c6 ("nptl: Reinstate pthread_timedjoin_np as a cancellation point (BZ#24215)")
- c50e1c263ec15e98da3235e663049156fd1afcfa ("x86: Remove arch-specific low level lock implementation")

commit 60d5e40ab200033a982a9fd7594a1f83dcdb94a0
Author: Florian Weimer <fweimer@redhat.com>
Date: Wed Apr 21 19:49:51 2021 +0200

    x86: Remove low-level lock optimization

    The current approach is to do this optimizations at a higher level,
    in generic code, so that single-threaded cases can be specifically
    targeted.

    Furthermore, using IS_IN (libc) as a compile-time indicator that
    all locks are private is no longer correct once process-shared lock
    implementations are moved into libc.

    The generic <lowlevellock.h> is not compatible with assembler code
    (obviously), so it's necessary to remove two long-unused #includes.

    Reviewed-by: Adhemerval Zanella  <adhemerval.zanella@linaro.org>

```
(gdb) disassemble
Dump of assembler code for function pthread_join:
   0x00007ffab329ff70 <+0>:     push   %r15
   0x00007ffab329ff72 <+2>:     push   %r14
   0x00007ffab329ff74 <+4>:     push   %r13
   0x00007ffab329ff76 <+6>:     push   %r12
   0x00007ffab329ff78 <+8>:     push   %rbp    # 8 Byte
   0x00007ffab329ff79 <+9>:     push   %rbx    # 8 Byte     6 * 8 = 48 Bytes

   0x00007ffab329ff7a <+10>:    mov    %rdi,%rbx
   0x00007ffab329ff7d <+13>:    sub    $0x28,%rsp
   0x00007ffab329ff81 <+17>:    mov    0x2d0(%rdi),%eax
   0x00007ffab329ff87 <+23>:    test   %eax,%eax
   0x00007ffab329ff89 <+25>:    js     0x7ffab32a00a9 <pthread_join+313>
   0x00007ffab329ff8f <+31>:    cmp    %rdi,0x628(%rdi)
   0x00007ffab329ff96 <+38>:    mov    $0x16,%eax
   0x00007ffab329ff9b <+43>:    je     0x7ffab32a0057 <pthread_join+231>
   0x00007ffab329ffa1 <+49>:    mov    %rsi,%r13
   0x00007ffab329ffa4 <+52>:    mov    %fs:0x10,%rbp
   0x00007ffab329ffad <+61>:    nop
   0x00007ffab329ffae <+62>:    lea    0x628(%rdi),%r12
   0x00007ffab329ffb5 <+69>:    lea    -0x6c(%rip),%rsi        # 0x7ffab329ff50 <cleanup>
   0x00007ffab329ffbc <+76>:    mov    %rsp,%rdi
   0x00007ffab329ffbf <+79>:    mov    %rsp,%r15
   0x00007ffab329ffc2 <+82>:    mov    %r12,%rdx
   0x00007ffab329ffc5 <+85>:    callq  0x7ffab32a5070 <_pthread_cleanup_push>
   0x00007ffab329ffca <+90>:    callq  0x7ffab32a5440 <__pthread_enable_asynccancel>
   0x00007ffab329ffcf <+95>:    cmp    %rbp,%rbx
   0x00007ffab329ffd2 <+98>:    mov    %eax,%r8d
   0x00007ffab329ffd5 <+101>:   je     0x7ffab32a007d <pthread_join+269>
   0x00007ffab329ffdb <+107>:   cmp    %rbx,0x628(%rbp)
   0x00007ffab329ffe2 <+114>:   je     0x7ffab32a0070 <pthread_join+256>
   0x00007ffab329ffe8 <+120>:   xor    %eax,%eax
   0x00007ffab329ffea <+122>:   lock cmpxchg %rbp,(%r12)
   0x00007ffab329fff0 <+128>:   jne    0x7ffab32a00b0 <pthread_join+320>
   0x00007ffab329fff6 <+134>:   mov    0x2d0(%rbx),%ecx
   0x00007ffab329fffc <+140>:   test   %ecx,%ecx
   0x00007ffab329fffe <+142>:   mov    %ecx,%edx
   0x00007ffab32a0000 <+144>:   je     0x7ffab32a001c <pthread_join+172>
   0x00007ffab32a0002 <+146>:   lea    0x2d0(%rbx),%rdi
   0x00007ffab32a0009 <+153>:   xor    %esi,%esi
   0x00007ffab32a000b <+155>:   xor    %r10,%r10
   0x00007ffab32a000e <+158>:   mov    $0xca,%rax
   0x00007ffab32a0015 <+165>:   syscall
=> 0x00007ffab32a0017 <+167>:   cmpl   $0x0,(%rdi)
```
