![](assets/Pasted%20image%2020220714150038.png)

```
data_errors=1
hd=default,vdbench=/root/vdbench,shell=ssh,user=root
sd=default,openflags=o_direct,size=5G,threads=32,journal=/root/vdbench/zyx1_journal
sd=sd1,lun=/dev/sda
sd=sd2,lun=/dev/sdb
sd=sd3,lun=/dev/vdb
wd=default,xfersize=(32k,20,64k,10,128k,30,256k,40)
wd=wd1,sd=sd1,seekpct=100
wd=wd2,sd=sd2,seekpct=100
wd=wd3,sd=sd3,seekpct=100
rd=default,iorate=max,elapsed=72000,interval=1,warmup=30,pause=10
rd=rd1,wd=wd*,rdpct=0
```

```c
/*                                                                            */
/* Write workload disk                                                        */
/*                                                                            */
extern jlong file_write(JNIEnv *env, jlong fhandle, jlong seek, jlong length, jlong buffer)
{
  int rc = pwrite64((int) fhandle, (void*) buffer, (size_t) length, (off64_t) seek);

  if (rc == -1)
  {
    if (errno == 0)
    {
      PTOD("Errno is zero after a failed read. Setting to 799");
      return 799;
    }
    return errno;
  }

  else if (rc != length)
  {
    PTOD1("Invalid byte count. Expecting %lld", length);
    PTOD1("but wrote only %d bytes.", rc);;
    return 798;
  }

  return 0;
}
```

![](assets/vdbench-key-block-header.png)

```
>>> datetime.fromtimestamp(0x00000181f6598f12/1000)
datetime.datetime(2022, 7, 13, 14, 57, 53, 426000)
```
