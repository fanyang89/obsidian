```
Dec 28 16:45:00 node-19-18 zbs_run_service.sh[4050723]: *** Aborted at 1703753094 (unix time) try "date -d @1703753094" if you are using GNU date ***
Dec 28 16:45:00 node-19-18 zbs_run_service.sh[4050723]: PC: @                0x0 (unknown)
Dec 28 16:45:09 node-19-18 zbs_run_service.sh[4050723]: *** SIGABRT (@0x3dcf23) received by PID 4050723 (TID 0xfffc2194e740) from PID 4050723; stack trace: ***
Dec 28 16:45:09 node-19-18 zbs_run_service.sh[4050723]:    @     0xfffc26e507bf ([vdso]+0x7be)
Dec 28 16:45:11 node-19-18 zbs_run_service.sh[4050723]:    @     0xfffc25d26570 gsignal
Dec 28 16:45:13 node-19-18 kernel: [3190886.129427] potentially unexpected fatal signal 6.
Dec 28 16:45:14 node-19-18 kernel: [3190886.129432] CPU: 24 PID: 4062199 Comm: watchdog Kdump: loaded Not tainted 4.19.90-2307.3.0.oe1.smartx.42.napiwarn.aarch64 #1
Dec 28 16:45:14 node-19-18 kernel: [3190886.129433] Hardware name: Huawei TaiShan 200 (Model 2280)/BC82AMDDA, BIOS 1.79 08/21/2021
Dec 28 16:45:14 node-19-18 kernel: [3190886.129435] pstate: 00000000 (nzcv daif -PAN -UAO)
Dec 28 16:45:14 node-19-18 kernel: [3190886.129436] pc : 0000fffc25d26570
Dec 28 16:45:14 node-19-18 kernel: [3190886.129436] lr : 0000fffc25d264fc
Dec 28 16:45:14 node-19-18 kernel: [3190886.129437] sp : 0000aaacdcb1fc50
Dec 28 16:45:14 node-19-18 kernel: [3190886.129438] x29: 0000aaacdcb1fc50 x28: 0000000000000384
Dec 28 16:45:14 node-19-18 kernel: [3190886.129440] x27: 0000aaacdc7bfa88 x26: 0000aaacdbedc1c8
Dec 28 16:45:14 node-19-18 kernel: [3190886.129452] x25: 0000000000000001 x24: 7fffffffffffffff
Dec 28 16:45:14 node-19-18 kernel: [3190886.129460] x23: 00000000be31797e x22: 0000aaacdcb1fed8
Dec 28 16:45:14 node-19-18 kernel: [3190886.129461] x21: 0000000000000000 x20: 0000000000000006
Dec 28 16:45:14 node-19-18 kernel: [3190886.129462] x19: 0000fffc25e5f000 x18: 00000000000007e6
Dec 28 16:45:14 node-19-18 kernel: [3190886.129463] x17: 0000fffc25d277c8 x16: 0000aaacd8b5e6f8
Dec 28 16:45:14 node-19-18 kernel: [3190886.129464] x15: 0000000000000014 x14: 0000000000000008
Dec 28 16:45:14 node-19-18 kernel: [3190886.129465] x13: ffffffffffffffff x12: ffffffffffffffff
Dec 28 16:45:14 node-19-18 kernel: [3190886.129467] x11: ffffffffffffffff x10: ffffffffffffffff
Dec 28 16:45:14 node-19-18 kernel: [3190886.129468] x9 : ffffffffffffffff x8 : 0000000000000087
Dec 28 16:45:14 node-19-18 kernel: [3190886.129469] x7 : ffffffffffffffff x6 : ffffffffffffffff
Dec 28 16:45:14 node-19-18 kernel: [3190886.129470] x5 : 0000aaacdcb1fc78 x4 : 0000000000000000
Dec 28 16:45:14 node-19-18 kernel: [3190886.129471] x3 : 0000000000000008 x2 : 0000000000000000
Dec 28 16:45:14 node-19-18 kernel: [3190886.129472] x1 : 0000aaacdcb1fc78 x0 : 0000000000000000
Dec 28 16:45:14 node-19-18 audit[4050723]: ANOM_ABEND auid=4294967295 uid=0 gid=0 ses=4294967295 pid=4050723 comm="watchdog" exe="/usr/sbin/zbs-metad" sig=6 res=1
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xfffc25d2791b abort
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xaaacd84ce517 (unknown)
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xaaacd85fd767 (unknown)
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xaaacd85faeef (unknown)
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xaaacd85fb04b (unknown)
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xaaacd85c039b (unknown)
Dec 28 16:45:14 node-19-18 zbs_run_service.sh[4050723]:    @     0xaaacd85c0403 (unknown)
```
