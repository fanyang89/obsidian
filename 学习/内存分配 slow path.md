```
# /proc/buddyinfo        64k    128k     256k  512k   1M     2M     4M
                         1 page  2       4      8      16     32     64
Node 0, zone    DMA32    105     17      6      5      4      2      1      2      1      2      2      2      2      0
Node 0, zone   Normal    191    501    306     56     36      8      7      2      0      0      0      0      0      0
Node 1, zone   Normal   1370    394    286     89     22      2      0      0      0      0      0      0      0      0
Node 2, zone   Normal    711   1289    284      2      0      0      0      0      0      0      0      0      0      0
Node 3, zone   Normal   3078   2551    600     66      7      3      0      0      0      0      0      0      0      0
```

```
root@oe5 [12:34:35] [/opt/llvm/llvm-project-10.0.1/build]
-> # cat /proc/buddyinfo
Node 0, zone      DMA      1      0      0      1      2      1      1      0      1      1      3
Node 0, zone    DMA32    378    281    117     81     88     21      3      2      1      4     50
Node 0, zone   Normal  12638  41840   2901    972    454    356    177     65     64      0      0

root@oe5 [12:34:39] [/opt/llvm/llvm-project-10.0.1/build]
-> # cat /proc/buddyinfo
                         4k      8k
Node 0, zone      DMA      1      0      0      1      2      1      1      0      1      1      3
Node 0, zone    DMA32    383    281    117     81     88     21      3      2      1      4     50
Node 0, zone   Normal   6291  42324   2901   1001    453    356    177     65     63      0      0
```
