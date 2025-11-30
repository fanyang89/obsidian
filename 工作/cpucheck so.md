```
cpucheck for file libcrypto.so.1.1 with x86_64-intel-sandybridge
invalid aes instructions found: aesdec, vaesdec, vaesdeclast, aesenclast, vaesenc, aesdeclast, vaesenclast, aesenc
[FAIL] cpucheck take: 11ms with error: instructions check failed
--
cpucheck for file librte_eal.so.23 with x86_64-intel-sandybridge
invalid instructions found: umwait, tpause, umonitor
[FAIL] cpucheck take: 89ms with error: instructions check failed
--
cpucheck for file libsystemd.so with x86_64-intel-sandybridge
invalid instructions found: rdrand
[FAIL] cpucheck take: 164ms with error: instructions check failed
--
cpucheck for file libzstd.so.1.4.5 with x86_64-intel-sandybridge
invalid bmi2 instructions found: sarx, shlx, shrx
[FAIL] cpucheck take: 302ms with error: instructions check failed

[root@1cfc999fd426 build]# rpm -qa | grep systemd-libs
systemd-libs-243-60.oe1.x86_64
```
