#java #jvm
https://docs.oracle.com/javase/8/docs/technotes/guides/vm/class-data-sharing.html

```
/usr/java/jdk1.8.0_131/jre/lib/amd64/server/classes.jsa
```

```
[root@mazg-smtxzbs550-upgradeX0701114342X1 13:03:37 jdk1.8.0_131]$ java -Xshare:dump
Allocated shared space: 37871616 bytes at 0x0000000800000000
Loading classes to share ...
Preload Warning: Cannot find sun/security/provider/DSA$LegacyDSA
Loading classes to share: done.
Rewriting and linking classes ...
Rewriting and linking classes: done
Number of classes 2572
    instance classes   =  2558
    obj array classes  =     6
    type array classes =     8
Calculating fingerprints ... done.
Removing unshareable information ... done.
Shared Lookup Cache Table Buckets = 8216 bytes
Shared Lookup Cache Table Body = 65184 bytes
ro space:   7333928 [ 36.3% of total] out of  16777216 bytes [43.7% used] at 0x0000000800000000
rw space:  11155728 [ 55.2% of total] out of  16777216 bytes [66.5% used] at 0x0000000801000000
md space:   1674880 [  8.3% of total] out of   4194304 bytes [39.9% used] at 0x0000000802000000
mc space:     34053 [  0.2% of total] out of    122880 bytes [27.7% used] at 0x0000000802400000
total   :  20198589 [100.0% of total] out of  37871616 bytes [53.3% used]
[root@mazg-smtxzbs550-upgradeX0701114342X1 13:04:07 jdk1.8.0_131]$
```

```
[root@mazg-smtxzbs550-upgradeX0701114342X1 13:04:57 java]$ rm -f /usr/java/jdk1.8.0_131/jre/lib/amd64/server/classes.jsa
You have new mail in /var/spool/mail/root
[root@mazg-smtxzbs550-upgradeX0701114342X1 13:05:17 java]$ time java -Xshare:dump
Allocated shared space: 37871616 bytes at 0x0000000800000000
Loading classes to share ...
Preload Warning: Cannot find sun/security/provider/DSA$LegacyDSA
Loading classes to share: done.
Rewriting and linking classes ...
Rewriting and linking classes: done
Number of classes 2572
    instance classes   =  2558
    obj array classes  =     6
    type array classes =     8
Calculating fingerprints ... done.
Removing unshareable information ... done.
Shared Lookup Cache Table Buckets = 8216 bytes
Shared Lookup Cache Table Body = 65184 bytes
ro space:   7333928 [ 36.3% of total] out of  16777216 bytes [43.7% used] at 0x0000000800000000
rw space:  11155728 [ 55.2% of total] out of  16777216 bytes [66.5% used] at 0x0000000801000000
md space:   1674880 [  8.3% of total] out of   4194304 bytes [39.9% used] at 0x0000000802000000
mc space:     34053 [  0.2% of total] out of    122880 bytes [27.7% used] at 0x0000000802400000
total   :  20198589 [100.0% of total] out of  37871616 bytes [53.3% used]

real    0m0.290s
user    0m0.244s
sys     0m0.039s
[root@mazg-smtxzbs550-upgradeX0701114342X1 13:05:26 java]$
```
