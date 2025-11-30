#oracle #Linux

```bash
crash> foreach UN ps -m | tail
[  0 00:00:00.008] [UN]  PID: 24140  TASK: ffff8b06e5e85140  CPU: 14  COMMAND: "practice task"
[  0 00:00:00.010] [UN]  PID: 17255  TASK: ffff8b1346e630c0  CPU: 13  COMMAND: "java"
[  0 00:00:00.013] [UN]  PID: 28836  TASK: ffff8b0c5337b0c0  CPU: 6   COMMAND: "cssdagent"
[  0 00:00:00.017] [UN]  PID: 28878  TASK: ffff8b0708368000  CPU: 0   COMMAND: "ocssd.bin"
[  0 00:00:00.018] [UN]  PID: 28870  TASK: ffff8b180436e180  CPU: 0   COMMAND: "ocssd.bin"
[  0 00:00:10.898] [UN]  PID: 11428  TASK: ffff8b019be31040  CPU: 2   COMMAND: "ps"
[  0 00:00:11.600] [UN]  PID: 28732  TASK: ffff8b213be69040  CPU: 2   COMMAND: "osysmond.bin"
[  0 00:00:14.668] [UN]  PID: 11422  TASK: ffff8b08a5c92080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:14.668] [UN]  PID: 29211  TASK: ffff8b1346e62080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:37.405] [UN]  PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"
```

```
crash> bt 28591
PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"
 #0 [ffff8b14196ffcc8] __schedule at ffffffffa53676c7
 #1 [ffff8b14196ffd50] schedule at ffffffffa5367bc9
 #2 [ffff8b14196ffd60] rwsem_down_write_failed at ffffffffa53694b5
 #3 [ffff8b14196ffdf0] call_rwsem_down_write_failed at ffffffffa4f869d7
 #4 [ffff8b14196ffe38] down_write at ffffffffa5366ecd
 #5 [ffff8b14196ffe50] vm_mmap_pgoff at ffffffffa4dd60e0
 #6 [ffff8b14196ffee0] sys_mmap_pgoff at ffffffffa4df07c6
 #7 [ffff8b14196fff40] sys_mmap at ffffffffa4c30c22
 #8 [ffff8b14196fff50] system_call_fastpath at ffffffffa5374ddb
    RIP: 00007fbec21b036a  RSP: 00007ffdde636b48  RFLAGS: 00010206
    RAX: 0000000000000009  RBX: 0000000000000000  RCX: 00007fbec0a24032
    RDX: 0000000000000003  RSI: 0000000000001000  RDI: 0000000000000000
    RBP: 0000000000001000   R8: ffffffffffffffff   R9: 0000000000000000
    R10: 0000000000000022  R11: 0000000000000246  R12: 0000000000000022
    R13: 0000000000000000  R14: ffffffffffffffff  R15: 0000000000000003
    ORIG_RAX: 0000000000000009  CS: 0033  SS: 002b
```

```c
/* All arch specific implementations share the same struct */
struct rw_semaphore {
  RH_KABI_REPLACE(long    count,
      atomic_long_t  count)
  raw_spinlock_t  wait_lock;
#if defined(CONFIG_RWSEM_SPIN_ON_OWNER) && !defined(__GENKSYMS__)
  struct optimistic_spin_queue osq; /* spinner MCS lock */
  struct slist_head  wait_list;
  /*
   * Write owner. Used as a speculative check to see
   * if the owner is running on the cpu.
   */
  struct task_struct  *owner;
#else
  struct list_head  wait_list;
#endif
#ifdef CONFIG_DEBUG_LOCK_ALLOC
  struct lockdep_map  dep_map;
#endif
};
```

```
crash> struct rw_semaphore -o
struct rw_semaphore {
       union {
   [0]     atomic_long_t count;
           struct {
   [0]         long count;
   [0]     } __UNIQUE_ID_rh_kabi_hide4;
           union {
               <no data fields>
           };
       };
   [8] raw_spinlock_t wait_lock;
  [12] struct optimistic_spin_queue osq;
  [16] struct slist_head wait_list;
  [24] struct task_struct *owner;
}
SIZE: 32
```

```
crash> bt -f
PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"
 #0 [ffff8b14196ffcc8] __schedule at ffffffffa53676c7
    ffff8b14196ffcd0: 0000000000000082 ffff8b14196fffd8
    ffff8b14196ffce0: ffff8b14196fffd8 ffff8b14196fffd8
    ffff8b14196ffcf0: 000000000001ab80 ffff8b02a94d30c0
    ffff8b14196ffd00: ffff8b06ba6af000 ffff8b14196ffd18
    ffff8b14196ffd10: ffffffffa4e640a4 00000000d4961099
    ffff8b14196ffd20: ffff8b1c44f18000 ffff8b113e3d0d00
    ffff8b14196ffd30: ffff8b113e3d0cf8 ffffffff00000001
    ffff8b14196ffd40: ffffffff00000000 ffff8b14196ffd58
    ffff8b14196ffd50: ffffffffa5367bc9
 #1 [ffff8b14196ffd50] schedule at ffffffffa5367bc9
    ffff8b14196ffd58: ffff8b14196ffde8 ffffffffa53694b5
 #2 [ffff8b14196ffd60] rwsem_down_write_failed at ffffffffa53694b5
    ffff8b14196ffd68: ffff8b14196ffd90 ffff8b113e3d0d08
    ffff8b14196ffd78: 0000000000000000 00000000d4961099
    ffff8b14196ffd88: ffff8b14196ffea4 ffff8b142776bde0
    ffff8b14196ffd98: ffff8b037bfb3d70 ffff8b1c44f18000
    ffff8b14196ffda8: 0000000000000000 ffff8b14196ffe88
    ffff8b14196ffdb8: 00000000d4961099 ffff8b113e3d0cf8
    ffff8b14196ffdc8: 0000000000000003 ffff8b113e3d0c80
    ffff8b14196ffdd8: ffff8b14196ffe98 0000000000000000
    ffff8b14196ffde8: ffff8b14196ffe30 ffffffffa4f869d7
 #3 [ffff8b14196ffdf0] call_rwsem_down_write_failed at ffffffffa4f869d7
    ffff8b14196ffdf8: ffff8b113e3d0cf8 0000000000001000
    ffff8b14196ffe08: 0000000000000000 0000000000000022
    ffff8b14196ffe18: ffff8b14196fffd8 0000000000000003
    ffff8b14196ffe28: ffff8b113e3d0cf8 ffff8b14196ffe48
    ffff8b14196ffe38: ffffffffa5366ecd
 #4 [ffff8b14196ffe38] down_write at ffffffffa5366ecd
    ffff8b14196ffe40: 0000000000000000 ffff8b14196ffed8
    ffff8b14196ffe50: ffffffffa4dd60e0
 #5 [ffff8b14196ffe50] vm_mmap_pgoff at ffffffffa4dd60e0
    ffff8b14196ffe58: 00000000669e5463 000000000bd54bd6
    ffff8b14196ffe68: 00000000669e5463 ffff8b113e3d0cf8
    ffff8b14196ffe78: 0000000000001000 0000000000000000
    ffff8b14196ffe88: 0000000000000022 00000000d4961099
    ffff8b14196ffe98: ffff8b14196ffe98 ffff8b14196ffe98
    ffff8b14196ffea8: 00000000d4961099 0000000000000022
    ffff8b14196ffeb8: 0000000000000000 0000000000000000
    ffff8b14196ffec8: 0000000000000003 0000000000000000
    ffff8b14196ffed8: ffff8b14196fff38 ffffffffa4df07c6
 #6 [ffff8b14196ffee0] sys_mmap_pgoff at ffffffffa4df07c6
    ffff8b14196ffee8: ffff8b1469292800 ffff8b1469292a70
    ffff8b14196ffef8: ffff8b1469292800 ffff8b1469292a70
    ffff8b14196fff08: 00000000d4961099 0000000000000000
    ffff8b14196fff18: 0000000000000000 0000000000000000
    ffff8b14196fff28: 0000000000000000 0000000000000000
    ffff8b14196fff38: ffff8b14196fff48 ffffffffa4c30c22
 #7 [ffff8b14196fff40] sys_mmap at ffffffffa4c30c22
    ffff8b14196fff48: 0000000000000000 ffffffffa5374ddb
 #8 [ffff8b14196fff50] system_call_fastpath at ffffffffa5374ddb
    RIP: 00007fbec21b036a  RSP: 00007ffdde636b48  RFLAGS: 00010206
    RAX: 0000000000000009  RBX: 0000000000000000  RCX: 00007fbec0a24032
    RDX: 0000000000000003  RSI: 0000000000001000  RDI: 0000000000000000
    RBP: 0000000000001000   R8: ffffffffffffffff   R9: 0000000000000000
    R10: 0000000000000022  R11: 0000000000000246  R12: 0000000000000022
    R13: 0000000000000000  R14: ffffffffffffffff  R15: 0000000000000003
    ORIG_RAX: 0000000000000009  CS: 0033  SS: 002b
```

```
CPU 1:
struct cpuinfo_x86 {
  x86 = 6 '\006',
  x86_vendor = 0 '\000',
  x86_model = 79 'O',
  x86_mask = 0 '\000',
  x86_tlbsize = 0,
  x86_virt_bits = 48 '0',
  x86_phys_bits = 45 '-',
  x86_coreid_bits = 3 '\003',
  extended_cpuid_level = 2147483656,
  cpuid_level = 13,
  x86_capability = {529267711, 739248128, 0, 567576832, 4294586883, 0, 289, 771885184, 0, 1844715, 1, 0, 0, 0, 4, 0, 0, 0, 3154117632, 0, 507904, 0, 0, 0, 0, 0, 0, 0, 0, 0},
  x86_vendor_id = "GenuineIntel\000\000\000",
  x86_model_id = "Intel(R) Xeon(R) Gold 6348 CPU @ 2.60GHz\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000",
  x86_cache_size = 43008,
  x86_cache_alignment = 64,
  x86_power = 256,
  loops_per_jiffy = 2593905,
  x86_max_cores = 8,
  apicid = 1,
  initial_apicid = 1,
  x86_clflush_size = 64,
  booted_cores = 8,
  phys_proc_id = 0,
  cpu_core_id = 1,
  {
    cu_id = 255 '\377',
    __UNIQUE_ID_rh_kabi_hide0 = {
      compute_unit_id = 255 '\377'
    },
    {<No data fields>}
  },
  cpu_index = 1,
  microcode = 218104741
}
```

![[Pasted image 20240731154250.png|600]]

```
#8 [ffff8b14196fff50] system_call_fastpath at ffffffffa5374ddb
RIP: 00007fbec21b036a  RSP: 00007ffdde636b48  RFLAGS: 00010206
RAX: 0000000000000009  RBX: 0000000000000000  RCX: 00007fbec0a24032
RDX: 0000000000000003  RSI: 0000000000001000  RDI: 0000000000000000
RBP: 0000000000001000   R8: ffffffffffffffff   R9: 0000000000000000
R10: 0000000000000022  R11: 0000000000000246  R12: 0000000000000022
R13: 0000000000000000  R14: ffffffffffffffff  R15: 0000000000000003
ORIG_RAX: 0000000000000009  CS: 0033  SS: 002b
```

可以观察到 `osysmond.bin` 在进行 `mmap` (`eax` =9)。

| reg | var   | value              | desc                        |
| --- | ----- | ------------------ | --------------------------- |
| rdi | addr  | 0x0                |                             |
| rsi | len   | 0x1000             | 4 KiB                       |
| rdx | prot  | 0x3                | PROT_READ & PROT_WRITE      |
| r10 | flags | 0x22               | MAP_ANONYMOUS & MAP_PRIVATE |
| r8  | fd    | 0xffffffffffffffff | -1                          |
| r9  | pgoff | 0x0                |                             |

> If _addr_ is NULL, then the kernel chooses the (page-aligned) address at which to create the mapping; this is the most portable method of creating a new mapping.

```c
unsigned long vm_mmap_pgoff(struct file *file, unsigned long addr,
  unsigned long len, unsigned long prot,
  unsigned long flag, unsigned long pgoff)
{
  unsigned long ret;
  struct mm_struct *mm = current->mm;
  unsigned long populate;
  LIST_HEAD(uf);

  ret = security_mmap_file(file, prot, flag);
  if (!ret) {
    down_write(&mm->mmap_sem);   // <---- 写锁
    ret = do_mmap_pgoff(file, addr, len, prot, flag, pgoff,
            &populate, &uf);
    up_write(&mm->mmap_sem);
    userfaultfd_unmap_complete(mm, &uf);
    if (populate)
      mm_populate(ret, populate);
  }
  return ret;
}
```

所以，`osysmond.bin` 是在进行 `mmap(len=4k)` 需要持有 `mm->mmap_sem` 进入 D 状态持续 37s.

```
PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"
```

```c
DECLARE_PER_CPU(struct task_struct *, current_task);

static __always_inline struct task_struct *get_current(void)
{
  return this_cpu_read_stable(current_task);
}

#define current get_current()
```

拿 CPU 1 上的 task

```
CPU 1:
struct cpuinfo_x86 {
  x86 = 6 '\006',
  x86_vendor = 0 '\000',
  x86_model = 79 'O',
  x86_mask = 0 '\000',
  x86_tlbsize = 0,
  x86_virt_bits = 48 '0',
  x86_phys_bits = 45 '-',
  x86_coreid_bits = 3 '\003',
  extended_cpuid_level = 2147483656,
  cpuid_level = 13,
  x86_capability = {529267711, 739248128, 0, 567576832, 4294586883, 0, 289, 771885184, 0, 1844715, 1, 0, 0, 0, 4, 0, 0, 0, 3154117632, 0, 507904, 0, 0, 0, 0, 0, 0, 0, 0, 0},
  x86_vendor_id = "GenuineIntel\000\000\000",
  x86_model_id = "Intel(R) Xeon(R) Gold 6348 CPU @ 2.60GHz\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000",
  x86_cache_size = 43008,
  x86_cache_alignment = 64,
  x86_power = 256,
  loops_per_jiffy = 2593905,
  x86_max_cores = 8,
  apicid = 1,
  initial_apicid = 1,
  x86_clflush_size = 64,
  booted_cores = 8,
  phys_proc_id = 0,
  cpu_core_id = 1,
  {
    cu_id = 255 '\377',
    __UNIQUE_ID_rh_kabi_hide0 = {
      compute_unit_id = 255 '\377'
    },
    {<No data fields>}
  },
  cpu_index = 1,
  microcode = 218104741
}
```

```c
crash> struct task_struct ffff8b1c44f18000
struct task_struct {
  mm = 0xffff8b113e3d0c80,
```

```c
mmap_sem = {
    {
      count = {
        counter = -4294967295
      },
      __UNIQUE_ID_rh_kabi_hide4 = {
        count = -4294967295
      },
      {<No data fields>}
    },
    wait_lock = {
      raw_lock = {
        val = {
          counter = 0
        }
      }
    },
    osq = {
      tail = {
        counter = 0
      }
    },
    wait_list = {
      next = 0xffff8b14196ffd90
    },
    owner = 0x1
  },
```

```
crash> foreach UN ps -m
[  0 00:00:00.005] [UN]  PID: 6092   TASK: ffff8b03a7c75140  CPU: 9   COMMAND: "basereport"
[  0 00:00:00.006] [UN]  PID: 17271  TASK: ffff8b1787bc1040  CPU: 12  COMMAND: "java"
[  0 00:00:00.007] [UN]  PID: 25073  TASK: ffff8b0b304e2080  CPU: 4   COMMAND: "kworker/4:2"
[  0 00:00:00.008] [UN]  PID: 24140  TASK: ffff8b06e5e85140  CPU: 14  COMMAND: "practice task"
[  0 00:00:00.010] [UN]  PID: 17255  TASK: ffff8b1346e630c0  CPU: 13  COMMAND: "java"
[  0 00:00:00.013] [UN]  PID: 28836  TASK: ffff8b0c5337b0c0  CPU: 6   COMMAND: "cssdagent"
[  0 00:00:00.017] [UN]  PID: 28878  TASK: ffff8b0708368000  CPU: 0   COMMAND: "ocssd.bin"
[  0 00:00:00.018] [UN]  PID: 28870  TASK: ffff8b180436e180  CPU: 0   COMMAND: "ocssd.bin"
[  0 00:00:10.898] [UN]  PID: 11428  TASK: ffff8b019be31040  CPU: 2   COMMAND: "ps"
[  0 00:00:11.600] [UN]  PID: 28732  TASK: ffff8b213be69040  CPU: 2   COMMAND: "osysmond.bin"
[  0 00:00:14.668] [UN]  PID: 11422  TASK: ffff8b08a5c92080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:14.668] [UN]  PID: 29211  TASK: ffff8b1346e62080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:37.405] [UN]  PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"
```

```
ps -m | grep '  CPU: 2   ' | grep -v 'IN'
```

```
crash> ps -m | grep '  CPU: 1   ' | grep -v 'IN'
[  0 00:00:00.000] [RU]  PID: 28364  TASK: ffff8b07053bd140  CPU: 1   COMMAND: "orarootagent.bi"
[  0 00:00:00.013] [RU]  PID: 29142  TASK: ffff8b11e9836180  CPU: 1   COMMAND: "octssd.bin"
[  0 00:00:00.015] [RU]  PID: 25376  TASK: ffff8b06c36b8000  CPU: 1   COMMAND: "ais_edr"
[  0 00:00:00.015] [RU]  PID: 28319  TASK: ffff8b0727a6c100  CPU: 1   COMMAND: "orarootagent.bi"
[  0 00:00:00.017] [RU]  PID: 11821  TASK: ffff8b1bcb7e4100  CPU: 1   COMMAND: "zabbix_agentd"
[  0 00:00:29.168] [RU]  PID: 13930  TASK: ffff8b18d0e330c0  CPU: 1   COMMAND: "hekad-daemon"
[  0 00:00:29.176] [RU]  PID: 5170   TASK: ffff8b1454eaa080  CPU: 1   COMMAND: "oraagent.bin"
[  0 00:00:29.176] [RU]  PID: 28600  TASK: ffff8b213dd2a080  CPU: 1   COMMAND: "cssdmonitor"
[  0 00:00:29.178] [RU]  PID: 7201   TASK: ffff8b186cf7c100  CPU: 1   COMMAND: "ora_lreg_atcdbt"
[  0 00:00:29.178] [RU]  PID: 28877  TASK: ffff8b12c26430c0  CPU: 1   COMMAND: "ocssd.bin"
[  0 00:00:29.178] [RU]  PID: 28786  TASK: ffff8b0c5b7e9040  CPU: 1   COMMAND: "ocssd.bin"
[  0 00:00:31.289] [RU]  PID: 13     TASK: ffff8b02a9556180  CPU: 1   COMMAND: "watchdog/1"
[  0 00:00:32.285] [RU]  PID: 11400  TASK: ffff8b0cdcfdc100  CPU: 1   COMMAND: "oracle_11400_at"
[  0 00:00:35.206] [RU]  PID: 15982  TASK: ffff8b113e276180  CPU: 1   COMMAND: "rtkit-daemon"
[  0 00:00:37.405] [UN]  PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"
[  0 00:00:40.334] [RU]  PID: 28809  TASK: ffff8b02a963b0c0  CPU: 1   COMMAND: "ocssd.bin"
[237 08:43:18.571] [RU]  PID: 0      TASK: ffff8b02a94d30c0  CPU: 1   COMMAND: "swapper/1"
[237 08:43:18.571] [RU]  PID: 11434  TASK: ffff8b1344e46180  CPU: 1   COMMAND: "systemd"
```

```
PID: 0      TASK: ffff8b02a94d30c0  CPU: 1   COMMAND: "swapper/1"
#0 [ffff8b02a9543e48] __schedule at ffffffffa53676c7
#1 [ffff8b02a9543ed0] schedule_preempt_disabled at ffffffffa5368ae9
#2 [ffff8b02a9543ee0] cpu_startup_entry at ffffffffa4cfc3fa
#3 [ffff8b02a9543f28] start_secondary at ffffffffa4c57db7
#4 [ffff8b02a9543f50] start_cpu at ffffffffa4c000d5
```

```
crash> bt 15982
PID: 15982  TASK: ffff8b113e276180  CPU: 1   COMMAND: "rtkit-daemon"
 #0 [ffff8b0c6ce0b958] __schedule at ffffffffa53676c7
 #1 [ffff8b0c6ce0b9e8] schedule at ffffffffa5367bc9
 #2 [ffff8b0c6ce0b9f8] schedule_timeout at ffffffffa53656a1
 #3 [ffff8b0c6ce0baa0] io_schedule_timeout at ffffffffa536726d
 #4 [ffff8b0c6ce0bad0] io_schedule at ffffffffa5367308
 #5 [ffff8b0c6ce0bae0] bit_wait_io at ffffffffa5365cf1
 #6 [ffff8b0c6ce0baf8] __wait_on_bit at ffffffffa5365817
 #7 [ffff8b0c6ce0bb38] wait_on_page_bit_killable at ffffffffa4db89c3
 #8 [ffff8b0c6ce0bb90] __lock_page_or_retry at ffffffffa4db8a92
 #9 [ffff8b0c6ce0bbb0] filemap_fault at ffffffffa4db8ee0
#10 [ffff8b0c6ce0bc20] __xfs_filemap_fault at ffffffffc035ad0e [xfs]
#11 [ffff8b0c6ce0bc80] xfs_filemap_fault at ffffffffc035af0c [xfs]
#12 [ffff8b0c6ce0bc90] __do_fault at ffffffffa4de41da
#13 [ffff8b0c6ce0bd10] do_read_fault at ffffffffa4de478c
#14 [ffff8b0c6ce0bd70] handle_pte_fault at ffffffffa4de9134
#15 [ffff8b0c6ce0be08] handle_mm_fault at ffffffffa4debc6d
#16 [ffff8b0c6ce0beb0] __do_page_fault at ffffffffa536f5e3
#17 [ffff8b0c6ce0bf20] do_page_fault at ffffffffa536f915
#18 [ffff8b0c6ce0bf50] page_fault at ffffffffa536b758
    RIP: 00007f876ed1d9aa  RSP: 00007f876bf30558  RFLAGS: 00010286
    RAX: 00007f876bf30c80  RBX: 00000000fbad8000  RCX: 000055702a76a310
    RDX: 00007f876bf30c68  RSI: 00000000ffffffff  RDI: 000055702a76a310
    RBP: 00007f876bf30b50   R8: 0000000000000000   R9: 00007f876ecd514d
    R10: 000055702a76a310  R11: 0000000000000000  R12: 000055702a76a310
    R13: 00007f876bf30c68  R14: 000055702a76a310  R15: 00007f875c0008c0
    ORIG_RAX: ffffffffffffffff  CS: 0033  SS: 002b
```

```bash
crash> p vm_swappiness
vm_swappiness = $1 = 10
```

```
crash> p min_free_kbytes
min_free_kbytes = $2 = 524288  // 512MiB

crash> kmem -i
                 PAGES        TOTAL      PERCENTAGE
    TOTAL MEM  32965732     125.8 GB         ----
         FREE  1408915       5.4 GB    4% of TOTAL MEM
         USED  31556817     120.4 GB   95% of TOTAL MEM
       SHARED  13225402      50.5 GB   40% of TOTAL MEM
      BUFFERS     1563       6.1 MB    0% of TOTAL MEM
       CACHED  16817181      64.2 GB   51% of TOTAL MEM
         SLAB   505515       1.9 GB    1% of TOTAL MEM

   TOTAL HUGE  9699328        37 GB         ----
    HUGE FREE    27136       106 MB    0% of TOTAL HUGE

   TOTAL SWAP  1048575         4 GB         ----
    SWAP USED    11136      43.5 MB    1% of TOTAL SWAP
    SWAP FREE  1037439         4 GB   98% of TOTAL SWAP

 COMMIT LIMIT  12681777      48.4 GB         ----
    COMMITTED  10925567      41.7 GB   86% of TOTAL LIMIT
```

```
pages_low = pages_min*5/4    // 640MiB
pages_high = pages_min*3/2   // 768MiB
```

剩余内存大于页高阈值，说明剩余内存比较多，没有内存压力。
`swappiness` 的范围是 0-100，数值越大，越积极使用 Swap，也就是更倾向于回收匿名页；数值越小，越消极使用 Swap，也就是更倾向于回收文件页。

```
crash> ps -m | grep '  CPU: 2   ' | grep -v 'IN'
[  0 00:00:00.010] [RU]  PID: 29144  TASK: ffff8b11e9834100  CPU: 2   COMMAND: "octssd.bin"
[  0 00:00:09.543] [RU]  PID: 32307  TASK: ffff8b06012d6180  CPU: 2   COMMAND: "pool-ds_am"
[  0 00:00:09.543] [RU]  PID: 24918  TASK: ffff8b05d48c0000  CPU: 2   COMMAND: "pool-ds_am"
[  0 00:00:09.543] [RU]  PID: 29198  TASK: ffff8b147d720000  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:09.543] [RU]  PID: 796    TASK: ffff8b134ba4e180  CPU: 2   COMMAND: "scriptagent.bin"
[  0 00:00:09.543] [RU]  PID: 20     TASK: ffff8b02a958b0c0  CPU: 2   COMMAND: "ksoftirqd/2"
[  0 00:00:09.543] [RU]  PID: 11398  TASK: ffff8b06e6a54100  CPU: 2   COMMAND: "oracle_11398_at"
[  0 00:00:09.543] [RU]  PID: 11391  TASK: ffff8b0aad0d2080  CPU: 2   COMMAND: "oracle_11391_at"
[  0 00:00:09.543] [RU]  PID: 11389  TASK: ffff8b08757f5140  CPU: 2   COMMAND: "oracle_11389_at"
[  0 00:00:09.543] [RU]  PID: 11381  TASK: ffff8b032d6ca080  CPU: 2   COMMAND: "oracle_11381_at"
[  0 00:00:09.543] [RU]  PID: 11363  TASK: ffff8b0871f60000  CPU: 2   COMMAND: "oracle_11363_at"
[  0 00:00:09.543] [RU]  PID: 29672  TASK: ffff8b07013c0000  CPU: 2   COMMAND: "asm_clmn_+asm1"
[  0 00:00:09.543] [RU]  PID: 7197   TASK: ffff8b1c1fd85140  CPU: 2   COMMAND: "ora_reco_atcdbt"
[  0 00:00:09.543] [RU]  PID: 16089  TASK: ffff8b181f695140  CPU: 2   COMMAND: "ora_w00t_atcdbt"
[  0 00:00:09.543] [RU]  PID: 466    TASK: ffff8b05d4920000  CPU: 2   COMMAND: "kworker/2:0"
[  0 00:00:09.543] [RU]  PID: 8285   TASK: ffff8b213bc79040  CPU: 2   COMMAND: "ora_arc0_atcdbt"
[  0 00:00:09.543] [RU]  PID: 29814  TASK: ffff8b07a3626180  CPU: 2   COMMAND: "asm_scm0_+asm1"
[  0 00:00:09.543] [RU]  PID: 20040  TASK: ffff8b0175ffc100  CPU: 2   COMMAND: "UsmMonitor"
[  0 00:00:09.547] [RU]  PID: 28618  TASK: ffff8b1c927d4100  CPU: 2   COMMAND: "cssdmonitor"
[  0 00:00:09.547] [RU]  PID: 16918  TASK: ffff8b06eca01040  CPU: 2   COMMAND: "asm_ppa7_+asm1"
[  0 00:00:09.555] [RU]  PID: 29118  TASK: ffff8b071d2fd140  CPU: 2   COMMAND: "evmd.bin"
[  0 00:00:09.598] [RU]  PID: 28540  TASK: ffff8b1c44f1d140  CPU: 2   COMMAND: "evmlogger.bin"
[  0 00:00:09.876] [RU]  PID: 10817  TASK: ffff8b14783cb0c0  CPU: 2   COMMAND: "in:imjournal"
[  0 00:00:10.163] [RU]  PID: 9024   TASK: ffff8b0c73794100  CPU: 2   COMMAND: "agentproc"
[  0 00:00:10.172] [RU]  PID: 28880  TASK: ffff8b0708369040  CPU: 2   COMMAND: "ocssd.bin"
[  0 00:00:10.898] [UN]  PID: 11428  TASK: ffff8b019be31040  CPU: 2   COMMAND: "ps"
[  0 00:00:11.287] [RU]  PID: 18     TASK: ffff8b02a9589040  CPU: 2   COMMAND: "watchdog/2"
[  0 00:00:11.600] [UN]  PID: 28732  TASK: ffff8b213be69040  CPU: 2   COMMAND: "osysmond.bin"
[  0 00:00:11.600] [RU]  PID: 28280  TASK: ffff8b1c9274d140  CPU: 2   COMMAND: "ohasd.bin"
[  0 00:00:11.606] [RU]  PID: 28296  TASK: ffff8b0c737f1040  CPU: 2   COMMAND: "ohasd.bin"
[  0 00:00:12.727] [RU]  PID: 12916  TASK: ffff8b134f361040  CPU: 2   COMMAND: "gmain"
[  0 00:00:12.772] [RU]  PID: 13928  TASK: ffff8b1930d030c0  CPU: 2   COMMAND: "hekad-daemon"
[  0 00:00:12.782] [RU]  PID: 13925  TASK: ffff8b10aa192080  CPU: 2   COMMAND: "hekad-daemon"
[  0 00:00:12.905] [RU]  PID: 24117  TASK: ffff8b0632a04100  CPU: 2   COMMAND: "ais_core"
[  0 00:00:13.039] [RU]  PID: 17325  TASK: ffff8b0cde36b0c0  CPU: 2   COMMAND: "java"
[  0 00:00:14.668] [UN]  PID: 11422  TASK: ffff8b08a5c92080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:14.668] [UN]  PID: 29211  TASK: ffff8b1346e62080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:15.279] [RU]  PID: 10818  TASK: ffff8b0c742530c0  CPU: 2   COMMAND: "rs:main Q:Reg"
[  0 00:00:15.279] [RU]  PID: 5459   TASK: ffff8b113a189040  CPU: 2   COMMAND: "systemd-journal"
[  0 00:00:22.052] [RU]  PID: 29695  TASK: ffff8b1930d05140  CPU: 2   COMMAND: "oracle_29695_at"
[  0 00:00:39.672] [RU]  PID: 25954  TASK: ffff8b08757f2080  CPU: 2   COMMAND: "hekad"
[  0 00:01:02.403] [RU]  PID: 12933  TASK: ffff8b0c757fe180  CPU: 2   COMMAND: "llvmpipe-12"
[237 08:43:18.571] [RU]  PID: 0      TASK: ffff8b02a94d4100  CPU: 2   COMMAND: "swapper/2"
```

```
crash> foreach UN ps -m | tail
[  0 00:00:00.008] [UN]  PID: 24140  TASK: ffff8b06e5e85140  CPU: 14  COMMAND: "practice task"
[  0 00:00:00.010] [UN]  PID: 17255  TASK: ffff8b1346e630c0  CPU: 13  COMMAND: "java"
[  0 00:00:00.013] [UN]  PID: 28836  TASK: ffff8b0c5337b0c0  CPU: 6   COMMAND: "cssdagent"
[  0 00:00:00.017] [UN]  PID: 28878  TASK: ffff8b0708368000  CPU: 0   COMMAND: "ocssd.bin"
[  0 00:00:00.018] [UN]  PID: 28870  TASK: ffff8b180436e180  CPU: 0   COMMAND: "ocssd.bin"
[  0 00:00:10.898] [UN]  PID: 11428  TASK: ffff8b019be31040  CPU: 2   COMMAND: "ps"
[  0 00:00:11.600] [UN]  PID: 28732  TASK: ffff8b213be69040  CPU: 2   COMMAND: "osysmond.bin"
[  0 00:00:14.668] [UN]  PID: 11422  TASK: ffff8b08a5c92080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:14.668] [UN]  PID: 29211  TASK: ffff8b1346e62080  CPU: 2   COMMAND: "ologgerd"
[  0 00:00:37.405] [UN]  PID: 28591  TASK: ffff8b1c44f18000  CPU: 1   COMMAND: "osysmond.bin"

crash> bt 11422
PID: 11422  TASK: ffff8b08a5c92080  CPU: 2   COMMAND: "ologgerd"
 #0 [ffff8b09eba5bd28] __schedule at ffffffffa53676c7
 #1 [ffff8b09eba5bdb0] schedule at ffffffffa5367bc9
 #2 [ffff8b09eba5bdc0] rwsem_down_read_failed at ffffffffa53691fd
 #3 [ffff8b09eba5be48] call_rwsem_down_read_failed at ffffffffa4f869a8
 #4 [ffff8b09eba5be98] down_read at ffffffffa5366e80
 #5 [ffff8b09eba5beb0] __do_page_fault at ffffffffa536f7d8
 #6 [ffff8b09eba5bf20] do_page_fault at ffffffffa536f915
 #7 [ffff8b09eba5bf50] page_fault at ffffffffa536b758
    RIP: 00007f78d1e145ff  RSP: 00007f78b75e1d40  RFLAGS: 00010202
    RAX: 00007f7878a93698  RBX: 00007f787a21a850  RCX: 00007f78b75e2b65
    RDX: 00007f78b75e33f0  RSI: 0000000000000000  RDI: 00007f787a21a850
    RBP: 00007f78b75e2ac0   R8: 00000000ffffffff   R9: 0000000000000001
    R10: 0000000000000000  R11: 00007f78ce571e90  R12: 00007f78b75e33f0
    R13: 0000000000000000  R14: 00007f78b75e2ec8  R15: 00007f78b75e2b20
    ORIG_RAX: ffffffffffffffff  CS: 0033  SS: 002b

crash> bt 29211
PID: 29211  TASK: ffff8b1346e62080  CPU: 2   COMMAND: "ologgerd"
 #0 [ffff8b1b8eec3cc8] __schedule at ffffffffa53676c7
 #1 [ffff8b1b8eec3d50] schedule at ffffffffa5367bc9
 #2 [ffff8b1b8eec3d60] rwsem_down_write_failed at ffffffffa53694b5
 #3 [ffff8b1b8eec3df0] call_rwsem_down_write_failed at ffffffffa4f869d7
 #4 [ffff8b1b8eec3e38] down_write at ffffffffa5366ecd
 #5 [ffff8b1b8eec3e50] vm_mmap_pgoff at ffffffffa4dd60e0
 #6 [ffff8b1b8eec3ee0] sys_mmap_pgoff at ffffffffa4df07c6
 #7 [ffff8b1b8eec3f40] sys_mmap at ffffffffa4c30c22
 #8 [ffff8b1b8eec3f50] system_call_fastpath at ffffffffa5374ddb
    RIP: 00007f78ce4e736a  RSP: 00007f78b7ae7e98  RFLAGS: 00010206
    RAX: 0000000000000009  RBX: 0000000000000000  RCX: 00007f78ccb41032
    RDX: 0000000000000003  RSI: 0000000000001000  RDI: 0000000000000000
    RBP: 0000000000001000   R8: ffffffffffffffff   R9: 0000000000000000
    R10: 0000000000000022  R11: 0000000000000246  R12: 0000000000000022
    R13: 0000000000000000  R14: ffffffffffffffff  R15: 0000000000000003
    ORIG_RAX: 0000000000000009  CS: 0033  SS: 002b
```

| reg | var   | value              | desc                        |
| --- | ----- | ------------------ | --------------------------- |
| rdi | addr  | 0x0                |                             |
| rsi | len   | 0x1000             | 4 KiB                       |
| rdx | prot  | 0x3                | PROT_READ & PROT_WRITE      |
| r10 | flags | 0x22               | MAP_ANONYMOUS & MAP_PRIVATE |
| r8  | fd    | 0xffffffffffffffff | -1                          |
| r9  | pgoff | 0x0                |                             |
