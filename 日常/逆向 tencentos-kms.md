
```
fanmi@ds [19:37:03] [~/workspace/tencentos-kms]
-> % nm -D ./usr/bin/ts-kms-cli | grep "^.*_Z" | head -5

fanmi@ds [19:37:28] [~/workspace/tencentos-kms]
-> % go version ./usr/bin/ts-kms-cli
./usr/bin/ts-kms-cli: could not read Go build info from ./usr/bin/ts-kms-cli: not a Go executable

fanmi@ds [19:37:34] [~/workspace/tencentos-kms]
-> % go version ./usr/bin/ts-kms-cli
./usr/bin/ts-kms-cli: could not read Go build info from ./usr/bin/ts-kms-cli: not a Go executable

fanmi@ds [19:37:41] [~/workspace/tencentos-kms]
-> % strings ./usr/bin/ts-kms-cli | grep -E "(GCC|gcc|clang)" | head -3
GCC: (GNU) 8.5.0 20210514 (Tencent 8.5.0-23)
GCC: (GNU) 8.5.0 20210514 (Tencent 8.5.0-26)
annobin gcc 8.5.0 20210514

fanmi@ds [19:37:45] [~/workspace/tencentos-kms]
-> % strings ./usr/bin/ts-kms-cli | grep -i "version\|build" | head -5
version:%s
	Command version
openssl-version
/builddir/build/BUILD/tencentos-kms-2.0.1/sysroot/lib64/ossl-modules
documentVersion

fanmi@ds [19:37:53] [~/workspace/tencentos-kms]
-> % objdump -t ./usr/bin/ts-kms-cli | grep -E "main|\.text" | head -5
000000000040441f l       .text	0000000000000000              .hidden .annobin_init.c
000000000040441f l       .text	0000000000000000              .hidden .annobin_init.c_end
00000000004043ea l       .text	0000000000000000              .hidden .annobin_init.c.hot
00000000004043ea l       .text	0000000000000000              .hidden .annobin_init.c_end.hot
0000000000404000 l       .text	0000000000000000              .hidden .annobin_init.c.unlikely
```

