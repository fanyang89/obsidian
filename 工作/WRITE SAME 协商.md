```bash
# SAS Device /usr/bin/sg_vpd --page=0xb0 /dev/sdb | less
Block limits VPD page (SBC):
  Write same no zero (WSNZ): 0
  Maximum compare and write length: 0 blocks
  Optimal transfer length granularity: 0 blocks
  Maximum transfer length: 0 blocks
  Optimal transfer length: 0 blocks
  Maximum prefetch length: 0 blocks
  Maximum unmap LBA count: 262143
  Maximum unmap block descriptor count: 32
  Optimal unmap granularity: 1
  Unmap granularity alignment valid: 0
  Unmap granularity alignment: 0
  Maximum write same length: 0x0 blocks

# Dogfood 17.95 /usr/bin/sg_vpd --page=0xb0 /dev/sdi
Block limits VPD page (SBC):
  Write same no zero (WSNZ): 1
  Maximum compare and write length: 1 blocks
  Optimal transfer length granularity: 8 blocks
  Maximum transfer length: 2560 blocks
  Optimal transfer length: 2560 blocks
  Maximum prefetch length: 0 blocks
  Maximum unmap LBA count: 2048
  Maximum unmap block descriptor count: 1
  Optimal unmap granularity: 512
  Unmap granularity alignment valid: 1
  Unmap granularity alignment: 512
  Maximum write same length: 0x80000 blocks

# ESXi /usr/bin/sg_vpd --page=0xb0 /dev/sdb
Block limits VPD page (SBC):
  Write same non-zero (WSNZ): 1
  Maximum compare and write length: 0 blocks [Command not implemented]
  Optimal transfer length granularity: 0 blocks [not reported]
  Maximum transfer length: 0 blocks [not reported]
  Optimal transfer length: 0 blocks [not reported]
  Maximum prefetch transfer length: 0 blocks [ignored]
  Maximum unmap LBA count: 65536
  Maximum unmap block descriptor count: 64
  Optimal unmap granularity: 2048 blocks
  Unmap granularity alignment valid: true
  Unmap granularity alignment: 0
  Maximum write same length: 0x2000 blocks
  Maximum atomic transfer length: 0 blocks [not reported]
  Atomic alignment: 0 [unaligned atomic writes permitted]
  Atomic transfer length granularity: 0 [no granularity requirement
  Maximum atomic transfer length with atomic boundary: 0 blocks [not reported]
  Maximum atomic boundary size: 0 blocks [can only write atomic 1 block]
```
