## TODO

- 关注的文件系统：xfs、ext4、NTFS
- 测试基准性能

## 背景

```
ID                                    Name                                  Parent ID                             Pool Name                           Size        UniqueSize    Shared Size    Creation Time                  Status           Replica Number  Thin Provision    Read Only    Description    Alloc Even      Clone count    Prefer CID
------------------------------------  ------------------------------------  ------------------------------------  ----------------------------------  ----------  -------------  -------------  -----------------------------  -------------  ----------------  ----------------  -----------  -------------  ------------  -------------  ------------
2cb34ff8-9eca-455c-b384-bc5c65b90cc9  2cb34ff8-9eca-455c-b384-bc5c65b90cc9  45894d2d-af7e-4751-a1cd-80ed0c13b892  zbs-iscsi-datastore-1646105297939k  20.00 GB    12.50 GB       0.00 B         2022-03-01 11:28:18.44838442   VOLUME_ONLINE                 1  True              False                       False                     0    0
45db2560-8b97-40ae-a156-d4111cf26c1f  45db2560-8b97-40ae-a156-d4111cf26c1f  45894d2d-af7e-4751-a1cd-80ed0c13b892  zbs-iscsi-datastore-1646105297939k  20.00 GB    4.75 GB        0.00 B         2022-03-01 11:28:18.34450520   VOLUME_ONLINE                 1  True              False                       False                     0    0
d57b8e59-844f-4436-a129-063dd7ca92ea  d57b8e59-844f-4436-a129-063dd7ca92ea  45894d2d-af7e-4751-a1cd-80ed0c13b892  zbs-iscsi-datastore-1646105297939k  40.00 GB    24.00 GB       0.00 B         2022-03-01 11:28:18.37772962   VOLUME_ONLINE                 1  True              False                       False                     0    0
```

```bash
[root@readahead-1 18:38:05 ~]$iomeshctl ra  streams
I0303 18:38:07.471235     479 client.go:188] connecting to 127.0.0.1:10200
streams {
volume_id=1d001153-4a8d-4b66-b270-f1d62fba791d, stream={ expired=1, start=0, ra_window=256, async=128, prev_offset=167172, prev_len=4, ctime=5439861237752191 }
volume_id=2cb34ff8-9eca-455c-b384-bc5c65b90cc9, stream={ expired=1, start=3812352, ra_window=2048, async=3813376, prev_offset=3812608, prev_len=256, ctime=5439672674392658 }
volume_id=d57b8e59-844f-4436-a129-063dd7ca92ea, stream={ expired=1, start=6917, ra_window=2048, async=7941, prev_offset=1370204, prev_len=48, ctime=5439271424218324 }
}
inflight {
}
blocked {
{ volume_id=d57b8e59-844f-4436-a129-063dd7ca92ea, expire_at=5439768016 } # 系统盘被禁用预读
}
```

## 性能对比

带预读

```bash
# curl -s http://192.168.91.19/fio/parsefio.py | python3 - ~/fio-ext4
name                direct  bs    rw          iodepth    bw(MB/s)     iops    lat(us)
----------------  --------  ----  --------  ---------  ----------  -------  ---------
10-256k-write            1  256k  write             1      210.82   804.22    1239.36
11-256k-read             1  256k  read              1      396.56  1512.77     656.88
12-256k-randread         1  256k  randread          1      195.24   744.76    1337.64
2-4k-read                1  4k    read              1       20.77  5071.25     195.18
3-4k-randread            1  4k    randread          1       10.18  2485.78     398.31
5-16k-read               1  16k   read              1       72.96  4452.98     222.38
6-16k-randread           1  16k   randread          1       34.45  2102.69     471.00
8-64k-read               1  64k   read              1      206.96  3157.89     313.64
9-64k-randread           1  64k   randread          1       83.32  1271.34     781.15

# curl -s http://192.168.91.19/fio/parsefio.py | python3 - ~/fio-xfs
name                direct  bs    rw          iodepth    bw(MB/s)     iops    lat(us)
----------------  --------  ----  --------  ---------  ----------  -------  ---------
10-256k-write            1  256k  write             1      153.02   583.74    1708.24
11-256k-read             1  256k  read              1      365.32  1393.58     713.45
12-256k-randread         1  256k  randread          1      208.51   795.41    1252.18
2-4k-read                1  4k    read              1       20.80  5077.85     195.02
3-4k-randread            1  4k    randread          1       10.33  2520.77     392.65
4-256k-write             1  256k  write             1      149.76   571.29    1745.69
5-16k-read               1  16k   read              1       72.08  4399.23     225.12
6-16k-randread           1  16k   randread          1       34.66  2115.19     468.34
8-64k-read               1  64k   read              1      206.16  3145.74     314.80
9-64k-randread           1  64k   randread          1       85.16  1299.49     764.12
```

无预读

```bash
# curl -s http://192.168.91.19/fio/parsefio.py | python3 - ~/fio-ext4
name                direct  bs    rw          iodepth    bw(MB/s)     iops    lat(us)
----------------  --------  ----  --------  ---------  ----------  -------  ---------
10-256k-write            1  256k  write             1      206.21   786.61    1266.50
11-256k-read             1  256k  read              1      252.41   962.85    1035.10
12-256k-randread         1  256k  randread          1      202.17   771.22    1291.50
2-4k-read                1  4k    read              1       18.81  4591.72     215.59
3-4k-randread            1  4k    randread          1       10.09  2462.33     402.33
4-256k-write             1  256k  write             1      205.45   783.72    1271.14
5-16k-read               1  16k   read              1       50.01  3052.30     324.41
6-16k-randread           1  16k   randread          1       35.66  2176.49     454.78
8-64k-read               1  64k   read              1      132.50  2021.75     491.20
9-64k-randread           1  64k   randread          1       85.17  1299.59     763.87

# curl -s http://192.168.91.19/fio/parsefio.py | python3 - ~/fio-xfs
name                direct  bs    rw          iodepth    bw(MB/s)     iops    lat(us)
----------------  --------  ----  --------  ---------  ----------  -------  ---------
10-256k-write            1  256k  write             1      155.29   592.37    1683.56
11-256k-read             1  256k  read              1      254.32   970.15    1026.53
12-256k-randread         1  256k  randread          1      209.34   798.56    1247.22
2-4k-read                1  4k    read              1       18.41  4493.88     220.29
3-4k-randread            1  4k    randread          1        9.75  2381.13     415.58
4-256k-write             1  256k  write             1      149.76   571.29    1745.69
5-16k-read               1  16k   read              1       60.70  3704.71     267.05
6-16k-randread           1  16k   randread          1       39.20  2392.73     414.15
8-64k-read               1  64k   read              1      163.27  2491.28     398.58
9-64k-randread           1  64k   randread          1       96.17  1467.48     676.73

# curl -s http://192.168.91.19/fio/parsefio.py | python3 - ~/fio-raw
name                direct  bs    rw          iodepth    bw(MB/s)     iops    lat(us)
----------------  --------  ----  --------  ---------  ----------  -------  ---------
10-256k-write            1  256k  write             1      206.19   786.56    1266.63
11-256k-read             1  256k  read              1      289.20  1103.19     902.29
12-256k-randread         1  256k  randread          1      312.47  1191.99     834.13
2-4k-read                1  4k    read              1       19.00  4639.27     213.58
3-4k-randread            1  4k    randread          1       11.50  2807.61     352.59
5-16k-read               1  16k   read              1       45.90  2801.41     353.43
6-16k-randread           1  16k   randread          1       39.90  2435.53     406.48
8-64k-read               1  64k   read              1      130.90  1997.40     496.50
9-64k-randread           1  64k   randread          1      115.39  1760.66     563.42
```

```
# lsblk -f -d -o name,rota,size
NAME  ROTA   SIZE
sda      0  59.6G
sdc      0 372.6G
sdd      0 372.6G

sde      1 931.5G
sdf      1 931.5G
sdg      1 931.5G
sdh      1 931.5G
```

## NBU 启动白屏问题

![[assets/Pasted image 20220311155636.png]]

```
sun.boot.class.path: /usr/java/jdk1.8.0_321-amd64/jre/lib/resources.jar:/usr/java/jdk1.8.0_321-amd64/jre/lib/rt.jar:/usr/java/jdk1.8.0_321-amd64/jre/lib/sunrsasign.jar:/usr/java/jdk1.8.0_321-amd64/jre/lib/jsse.jar:/usr/java/jdk1.8.0_321-amd64/jre/lib/jce.jar:/usr/java/jdk1.8.0_321-amd64/jre/lib/charsets.jar:/usr/java/jdk1.8.0_321-amd64/jre/lib/jfr.jar:/usr/java/jdk1.8.0_321-amd64/jre/classes
vrts.common.utilities.DEBUG_PROPERTIES: /usr/openv/java/Debug.properties
java.vendor: Oracle Corporation
vrts.common.server.LOG_CMDLINES: false
file.separator: /
java.vendor.url.bug: http://bugreport.sun.com/bugreport/
sun.io.unicode.encoding: UnicodeLittle
sun.cpu.endian: little
sun.cpu.isalist:
```

## 57.62 测试

![[assets/Pasted image 20220323185809.png]]

![[assets/Pasted image 20220323204051.png]]

## 2022-04-07

![[assets/Pasted image 20220407172930.png]]
31~34 UEFI

```
$ zbs-nfs inode show 96b2cdb8-56f4-4cb5
-----------  ------------------------------------
id           96b2cdb8-56f4-4cb5
name         fio-zbs_1-flat.vmdk
pool         03888a96-a517-42cb-9f82-72e31272a062
preallocate  False
volume       96b2cdb8-56f4-4cb5-b48f-354224fdd842
type         FILE
mode         384
uid          0
gid          0
size         40.00 GB
unique size  40.00 GB
shared size  0.00 B
-----------  ------------------------------------
```

```
ID                                    Name                Parent ID                             Size        Unique Size    Shared Size    Creation Time                  Status           Replica Number  Thin Provision    Read Only    Description    Alloc Even      Clone count    Prefer CID
------------------------------------  ------------------  ------------------------------------  ----------  -------------  -------------  -----------------------------  -------------  ----------------  ----------------  -----------  -------------  ------------  -------------  ------------
96b2cdb8-56f4-4cb5-b48f-354224fdd842  96b2cdb8-56f4-4cb5  03888a96-a517-42cb-9f82-72e31272a062  40.00 GB    40.00 GB       0.00 B         2022-04-12 17:37:41.225397754  VOLUME_ONLINE                 1  True              False                       False                     0             0
```

```
$ zbs-meta volume show share 96b2cdb8-56f4-4cb5
--------------  ------------------------------------
ID              96b2cdb8-56f4-4cb5-b48f-354224fdd842
Name            96b2cdb8-56f4-4cb5
Parent ID       03888a96-a517-42cb-9f82-72e31272a062
Size            40.00 GB
Unique Size     40.00 GB
Shared Size     0.00 B
Creation Time   2022-04-12 17:37:41.225397754
Status          VOLUME_ONLINE
Replica Number  1
Thin Provision  True
Read Only       False
Description
Alloc Even      False
Clone count     0
Iops            0
Iops Read       0
Iops Write      0
Bps             0
Bps Read        0
Bps Write       0
Stripe Num      4
Stripe Size     262144
Prefer CID      0
--------------  ------------------------------------
```

```
$zbs-meta volume show share 96b2cdb8-56f4-4cb5 --show_replica_distribution
--------------  ------------------------------------
ID              96b2cdb8-56f4-4cb5-b48f-354224fdd842
Name            96b2cdb8-56f4-4cb5
Parent ID       03888a96-a517-42cb-9f82-72e31272a062
Size            40.00 GB
Unique Size     40.00 GB
Shared Size     0.00 B
Creation Time   2022-04-12 17:37:41.225397754
Status          VOLUME_ONLINE
Replica Number  1
Thin Provision  True
Read Only       False
Description
Alloc Even      False
Clone count     0
Iops            0
Iops Read       0
Iops Write      0
Bps             0
Bps Read        0
Bps Write       0
Stripe Num      4
Stripe Size     262144
Prefer CID      0
--------------  ------------------------------------
  chunk_id    total_replica_count    alive_replica_count
----------  ---------------------  ---------------------
         2                    160                    160
```

```
$ zbs-meta volume show share 96b2cdb8-56f4-4cb5 --show_pextents
--------------  ------------------------------------
ID              96b2cdb8-56f4-4cb5-b48f-354224fdd842
Name            96b2cdb8-56f4-4cb5
Parent ID       03888a96-a517-42cb-9f82-72e31272a062
Size            40.00 GB
Unique Size     40.00 GB
Shared Size     0.00 B
Creation Time   2022-04-12 17:37:41.225397754
Status          VOLUME_ONLINE
Replica Number  1
Thin Provision  True
Read Only       False
Description
Alloc Even      False
Clone count     0
Iops            0
Iops Read       0
Iops Write      0
Bps             0
Bps Read        0
Bps Write       0
Stripe Num      4
Stripe Size     262144
Prefer CID      0
--------------  ------------------------------------
  ID  Replica    Alive Replica    Ever Exist    Is Garbage      Origin PExtent    Expected Replica num    Epoch    Prefer Local    Lease Owner
----  ---------  ---------------  ------------  ------------  ----------------  ----------------------  -------  --------------  -------------
 860  [2]        [2]              True          False                        0                       1     1795               2              2
 861  [2]        [2]              True          False                        0                       1     3814               2              2
 862  [2]        [2]              True          False                        0                       1     3815               2              2
 863  [2]        [2]              True          False                        0                       1     3816               2              2
 864  [2]        [2]              True          False                        0                       1     3817               2              2
 865  [2]        [2]              True          False                        0                       1     3818               2              2
 866  [2]        [2]              True          False                        0                       1     3819               2              2
 867  [2]        [2]              True          False                        0                       1     3820               2              2
 868  [2]        [2]              True          False                        0                       1     3821               2              2
 869  [2]        [2]              True          False                        0                       1     3822               2              2
 870  [2]        [2]              True          False                        0                       1     3823               2              2
 871  [2]        [2]              True          False                        0                       1     3824               2              2
 872  [2]        [2]              True          False                        0                       1     3825               2              2
 873  [2]        [2]              True          False                        0                       1     3826               2              2
 874  [2]        [2]              True          False                        0                       1     3827               2              2
 875  [2]        [2]              True          False                        0                       1     3828               2              2
 876  [2]        [2]              True          False                        0                       1     3829               2              2
 877  [2]        [2]              True          False                        0                       1     3830               2              2
 878  [2]        [2]              True          False                        0                       1     3831               2              2
 879  [2]        [2]              True          False                        0                       1     3832               2              2
 880  [2]        [2]              True          False                        0                       1     3833               2              2
 881  [2]        [2]              True          False                        0                       1     3834               2              2
 882  [2]        [2]              True          False                        0                       1     3835               2              2
 883  [2]        [2]              True          False                        0                       1     3836               2              2
 884  [2]        [2]              True          False                        0                       1     3837               2              2
 885  [2]        [2]              True          False                        0                       1     3838               2              2
 886  [2]        [2]              True          False                        0                       1     3839               2              2
 887  [2]        [2]              True          False                        0                       1     3840               2              2
 888  [2]        [2]              True          False                        0                       1     3841               2              2
 889  [2]        [2]              True          False                        0                       1     3842               2              2
 890  [2]        [2]              True          False                        0                       1     3843               2              2
 891  [2]        [2]              True          False                        0                       1     3844               2              2
 892  [2]        [2]              True          False                        0                       1     3845               2              2
 893  [2]        [2]              True          False                        0                       1     3846               2              2
 894  [2]        [2]              True          False                        0                       1     3847               2              2
 895  [2]        [2]              True          False                        0                       1     3848               2              2
 896  [2]        [2]              True          False                        0                       1     3849               2              2
 897  [2]        [2]              True          False                        0                       1     3850               2              2
 898  [2]        [2]              True          False                        0                       1     3851               2              2
 899  [2]        [2]              True          False                        0                       1     3852               2              2
 900  [2]        [2]              True          False                        0                       1     3853               2              2
 901  [2]        [2]              True          False                        0                       1     3854               2              2
 902  [2]        [2]              True          False                        0                       1     1822               2              2
 903  [2]        [2]              True          False                        0                       1     3855               2              2
 904  [2]        [2]              True          False                        0                       1     3856               2              2
 905  [2]        [2]              True          False                        0                       1     3857               2              2
 906  [2]        [2]              True          False                        0                       1     3858               2              2
 907  [2]        [2]              True          False                        0                       1     3859               2              2
 908  [2]        [2]              True          False                        0                       1     3860               2              2
 909  [2]        [2]              True          False                        0                       1     3861               2              2
 910  [2]        [2]              True          False                        0                       1     3862               2              2
 911  [2]        [2]              True          False                        0                       1     3863               2              2
 912  [2]        [2]              True          False                        0                       1     3864               2              2
 913  [2]        [2]              True          False                        0                       1     3865               2              2
 914  [2]        [2]              True          False                        0                       1     3866               2              2
 915  [2]        [2]              True          False                        0                       1     3867               2              2
 916  [2]        [2]              True          False                        0                       1     3868               2              2
 917  [2]        [2]              True          False                        0                       1     3869               2              2
 918  [2]        [2]              True          False                        0                       1     3870               2              2
 919  [2]        [2]              True          False                        0                       1     3871               2              2
 920  [2]        [2]              True          False                        0                       1     3872               2              2
 921  [2]        [2]              True          False                        0                       1     3873               2              2
 922  [2]        [2]              True          False                        0                       1     3874               2              2
 923  [2]        [2]              True          False                        0                       1     3875               2              2
 924  [2]        [2]              True          False                        0                       1     3876               2              2
 925  [2]        [2]              True          False                        0                       1     3877               2              2
 926  [2]        [2]              True          False                        0                       1     3878               2              2
 927  [2]        [2]              True          False                        0                       1     3879               2              2
 928  [2]        [2]              True          False                        0                       1     3880               2              2
 929  [2]        [2]              True          False                        0                       1     3881               2              2
 930  [2]        [2]              True          False                        0                       1     3882               2              2
 931  [2]        [2]              True          False                        0                       1     3883               2              2
 932  [2]        [2]              True          False                        0                       1     3884               2              2
 933  [2]        [2]              True          False                        0                       1     3885               2              2
 934  [2]        [2]              True          False                        0                       1     3886               2              2
 935  [2]        [2]              True          False                        0                       1     3887               2              2
 936  [2]        [2]              True          False                        0                       1     3888               2              2
 937  [2]        [2]              True          False                        0                       1     3889               2              2
 938  [2]        [2]              True          False                        0                       1     3890               2              2
 939  [2]        [2]              True          False                        0                       1     3891               2              2
 940  [2]        [2]              True          False                        0                       1     1818               2              2
 941  [2]        [2]              True          False                        0                       1     1819               2              2
 942  [2]        [2]              True          False                        0                       1     1820               2              2
 943  [2]        [2]              True          False                        0                       1     1821               2              2
 944  [2]        [2]              True          False                        0                       1     3892               2              2
 945  [2]        [2]              True          False                        0                       1     3893               2              2
 946  [2]        [2]              True          False                        0                       1     3894               2              2
 947  [2]        [2]              True          False                        0                       1     3895               2              2
 948  [2]        [2]              True          False                        0                       1     3896               2              2
 949  [2]        [2]              True          False                        0                       1     3897               2              2
 950  [2]        [2]              True          False                        0                       1     3898               2              2
 951  [2]        [2]              True          False                        0                       1     3899               2              2
 952  [2]        [2]              True          False                        0                       1     3900               2              2
 953  [2]        [2]              True          False                        0                       1     3901               2              2
 954  [2]        [2]              True          False                        0                       1     3902               2              2
 955  [2]        [2]              True          False                        0                       1     3903               2              2
 956  [2]        [2]              True          False                        0                       1     3904               2              2
 957  [2]        [2]              True          False                        0                       1     3905               2              2
 958  [2]        [2]              True          False                        0                       1     3906               2              2
 959  [2]        [2]              True          False                        0                       1     3907               2              2
 960  [2]        [2]              True          False                        0                       1     3908               2              2
 961  [2]        [2]              True          False                        0                       1     3909               2              2
 962  [2]        [2]              True          False                        0                       1     3910               2              2
 963  [2]        [2]              True          False                        0                       1     3911               2              2
 964  [2]        [2]              True          False                        0                       1     3912               2              2
 965  [2]        [2]              True          False                        0                       1     3913               2              2
 966  [2]        [2]              True          False                        0                       1     3914               2              2
 967  [2]        [2]              True          False                        0                       1     3915               2              2
 968  [2]        [2]              True          False                        0                       1     3916               2              2
 969  [2]        [2]              True          False                        0                       1     3917               2              2
 970  [2]        [2]              True          False                        0                       1     3918               2              2
 971  [2]        [2]              True          False                        0                       1     3919               2              2
 972  [2]        [2]              True          False                        0                       1     3920               2              2
 973  [2]        [2]              True          False                        0                       1     3921               2              2
 974  [2]        [2]              True          False                        0                       1     3922               2              2
 975  [2]        [2]              True          False                        0                       1     3923               2              2
 976  [2]        [2]              True          False                        0                       1     3924               2              2
 977  [2]        [2]              True          False                        0                       1     3925               2              2
 978  [2]        [2]              True          False                        0                       1     1823               2              2
 979  [2]        [2]              True          False                        0                       1     3926               2              2
 980  [2]        [2]              True          False                        0                       1     3927               2              2
 981  [2]        [2]              True          False                        0                       1     3928               2              2
 982  [2]        [2]              True          False                        0                       1     3929               2              2
 983  [2]        [2]              True          False                        0                       1     3930               2              2
 984  [2]        [2]              True          False                        0                       1     3931               2              2
 985  [2]        [2]              True          False                        0                       1     3932               2              2
 986  [2]        [2]              True          False                        0                       1     3933               2              2
 987  [2]        [2]              True          False                        0                       1     3934               2              2
 988  [2]        [2]              True          False                        0                       1     3935               2              2
 989  [2]        [2]              True          False                        0                       1     3936               2              2
 990  [2]        [2]              True          False                        0                       1     3937               2              2
 991  [2]        [2]              True          False                        0                       1     3938               2              2
 992  [2]        [2]              True          False                        0                       1     3939               2              2
 993  [2]        [2]              True          False                        0                       1     3940               2              2
 994  [2]        [2]              True          False                        0                       1     3941               2              2
 995  [2]        [2]              True          False                        0                       1     3942               2              2
 996  [2]        [2]              True          False                        0                       1     3943               2              2
 997  [2]        [2]              True          False                        0                       1     3944               2              2
 998  [2]        [2]              True          False                        0                       1     3945               2              2
 999  [2]        [2]              True          False                        0                       1     3946               2              2
1000  [2]        [2]              True          False                        0                       1     3947               2              2
1001  [2]        [2]              True          False                        0                       1     3948               2              2
1002  [2]        [2]              True          False                        0                       1     3949               2              2
1003  [2]        [2]              True          False                        0                       1     3950               2              2
1004  [2]        [2]              True          False                        0                       1     3951               2              2
1005  [2]        [2]              True          False                        0                       1     3952               2              2
1006  [2]        [2]              True          False                        0                       1     3953               2              2
1007  [2]        [2]              True          False                        0                       1     3954               2              2
1008  [2]        [2]              True          False                        0                       1     3955               2              2
1009  [2]        [2]              True          False                        0                       1     3956               2              2
1010  [2]        [2]              True          False                        0                       1     3957               2              2
1011  [2]        [2]              True          False                        0                       1     3958               2              2
1012  [2]        [2]              True          False                        0                       1     3959               2              2
1013  [2]        [2]              True          False                        0                       1     3960               2              2
1014  [2]        [2]              True          False                        0                       1     3961               2              2
1015  [2]        [2]              True          False                        0                       1     3962               2              2
1016  [2]        [2]              True          False                        0                       1     3963               2              2
1017  [2]        [2]              True          False                        0                       1     3964               2              2
1018  [2]        [2]              True          False                        0                       1     3965               2              2
1019  [2]        [2]              True          False                        0                       1     1794               2              2
```

```bash
zbs-meta volume show share 96b2cdb8-56f4-4cb5 --show_pextents | sed '/Lease Owner/,$!d' | tail -n +3 | awk '{print $1}' | tr '\n' ','

860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019
```

```bash
# 无预读 fio
# fio --name a --filename=$DEVICE_NAME --bs=256k --rw=read --ioengine=libaio --direct=1 --iodepth=1 --numjobs=1 --time_based --runtime=60s
a: (g=0): rw=read, bs=(R) 256KiB-256KiB, (W) 256KiB-256KiB, (T) 256KiB-256KiB, ioengine=libaio, iodepth=1
fio-3.19
Starting 1 process
Jobs: 1 (f=1): [R(1)][100.0%][r=216MiB/s][r=862 IOPS][eta 00m:00s]
a: (groupid=0, jobs=1): err= 0: pid=5947: Wed Apr 13 00:31:04 2022
  read: IOPS=745, BW=186MiB/s (195MB/s)(10.9GiB/60001msec)
    slat (usec): min=20, max=772, avg=29.76, stdev=12.18
    clat (usec): min=861, max=7106, avg=1303.97, stdev=559.06
     lat (usec): min=920, max=7130, avg=1335.19, stdev=558.66
    clat percentiles (usec):
     |  1.00th=[  947],  5.00th=[  971], 10.00th=[  988], 20.00th=[ 1012],
     | 30.00th=[ 1029], 40.00th=[ 1045], 50.00th=[ 1074], 60.00th=[ 1106],
     | 70.00th=[ 1221], 80.00th=[ 1516], 90.00th=[ 1942], 95.00th=[ 2278],
     | 99.00th=[ 3949], 99.50th=[ 4080], 99.90th=[ 5080], 99.95th=[ 5538],
     | 99.99th=[ 6587]
   bw (  KiB/s): min=141824, max=246802, per=100.00%, avg=191020.19, stdev=27857.72, samples=119
   iops        : min=  554, max=  964, avg=746.13, stdev=108.83, samples=119
  lat (usec)   : 1000=13.67%
  lat (msec)   : 2=79.07%, 4=6.44%, 10=0.82%
  cpu          : usr=1.14%, sys=2.54%, ctx=44729, majf=0, minf=76
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=44729,0,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
   READ: bw=186MiB/s (195MB/s), 186MiB/s-186MiB/s (195MB/s-195MB/s), io=10.9GiB (11.7GB), run=60001-60001msec

Disk stats (read/write):
  sdb: ios=44724/0, merge=0/0, ticks=58217/0, in_queue=58217, util=99.99%
```

```bash
# mmap 预读 fio，走系统预读
# fio --name a --filename=$DEVICE_NAME --bs=256k --rw=read --ioengine=libaio --direct=1 --iodepth=1 --numjobs=1 --time_based --runtime=60s
a: (g=0): rw=read, bs=(R) 256KiB-256KiB, (W) 256KiB-256KiB, (T) 256KiB-256KiB, ioengine=libaio, iodepth=1
fio-3.19
Starting 1 process
^Cbs: 1 (f=1): [R(1)][32.8%][r=171MiB/s][r=684 IOPS][eta 00m:41s]
fio: terminating on signal 2

a: (groupid=0, jobs=1): err= 0: pid=6024: Wed Apr 13 00:46:03 2022
  read: IOPS=760, BW=190MiB/s (199MB/s)(3674MiB/19320msec)
    slat (usec): min=21, max=316, avg=32.81, stdev=15.10
    clat (usec): min=407, max=21837, avg=1274.05, stdev=1375.64
     lat (usec): min=462, max=22163, avg=1308.42, stdev=1376.25
    clat percentiles (usec):
     |  1.00th=[  482],  5.00th=[  652], 10.00th=[  685], 20.00th=[  717],
     | 30.00th=[  734], 40.00th=[  750], 50.00th=[  775], 60.00th=[  816],
     | 70.00th=[  955], 80.00th=[ 1188], 90.00th=[ 2278], 95.00th=[ 5342],
     | 99.00th=[ 6325], 99.50th=[ 6980], 99.90th=[ 8848], 99.95th=[17171],
     | 99.99th=[19006]
   bw (  KiB/s): min=150227, max=238080, per=100.00%, avg=194786.87, stdev=21314.66, samples=38
   iops        : min=  586, max=  930, avg=760.79, stdev=83.28, samples=38
  lat (usec)   : 500=1.57%, 750=36.73%, 1000=35.62%
  lat (msec)   : 2=14.79%, 4=4.51%, 10=6.70%, 20=0.08%, 50=0.01%
  cpu          : usr=1.10%, sys=2.82%, ctx=14694, majf=0, minf=77
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=14694,0,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
   READ: bw=190MiB/s (199MB/s), 190MiB/s-190MiB/s (199MB/s-199MB/s), io=3674MiB (3852MB), run=19320-19320msec

Disk stats (read/write):
  sdb: ios=14591/0, merge=0/0, ticks=18573/0, in_queue=18573, util=99.65%

[root@RA-SCVM31 00:44:37 ~]$vmtouch ./96b2cdb8-56f4-4cb5.bin
           Files: 1
     Directories: 0
  Resident Pages: 0/10485760  0/40G  0%
         Elapsed: 0.29733 seconds
[root@RA-SCVM31 00:44:46 ~]$vmtouch ./96b2cdb8-56f4-4cb5.bin
           Files: 1
     Directories: 0
  Resident Pages: 74492/10485760  290M/40G  0.71%
         Elapsed: 0.31216 seconds
[root@RA-SCVM31 00:45:02 ~]$vmtouch ./96b2cdb8-56f4-4cb5.bin
           Files: 1
     Directories: 0
  Resident Pages: 180476/10485760  704M/40G  1.72%
         Elapsed: 0.30186 seconds
[root@RA-SCVM31 00:45:04 ~]$vmtouch ./96b2cdb8-56f4-4cb5.bin
           Files: 1
     Directories: 0
  Resident Pages: 827580/10485760  3G/40G  7.89%
         Elapsed: 0.34202 seconds
```

```bash
# mmap 预读 fio，走系统预读 预先 touch all area
[root@RA-SCVM31 00:46:10 ~]$vmtouch -t -v ./96b2cdb8-56f4-4cb5.bin
./96b2cdb8-56f4-4cb5.bin
[OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO] 10485760/10485760

           Files: 1
     Directories: 0
   Touched Pages: 10485760 (40G)
         Elapsed: 81.576 seconds
[root@RA-SCVM31 00:47:33 ~]$vmtouch ./96b2cdb8-56f4-4cb5.bin
           Files: 1
     Directories: 0
  Resident Pages: 10485760/10485760  40G/40G  100%
         Elapsed: 0.62486 seconds
[root@RA-SCVM31 00:47:50 ~]$free -mh
              total        used        free      shared  buff/cache   available
Mem:            47G        2.9G        2.9G         17M         41G         43G
Swap:            0B          0B          0B

# fio --name a --filename=$DEVICE_NAME --bs=256k --rw=read --ioengine=libaio --direct=1 --iodepth=1 --numjobs=1 --time_based --runtime=60s
a: (g=0): rw=read, bs=(R) 256KiB-256KiB, (W) 256KiB-256KiB, (T) 256KiB-256KiB, ioengine=libaio, iodepth=1
fio-3.19
Starting 1 process
Jobs: 1 (f=0): [f(1)][100.0%][r=312MiB/s][r=1249 IOPS][eta 00m:00s]
a: (groupid=0, jobs=1): err= 0: pid=6031: Wed Apr 13 00:49:52 2022
  read: IOPS=1230, BW=308MiB/s (323MB/s)(18.0GiB/60001msec)  # 195MB/s -> 199MB/s -> 323MB/s ~ 带宽 +66%
    slat (usec): min=21, max=337, avg=28.94, stdev= 9.89
    clat (usec): min=355, max=6577, avg=775.90, stdev=520.72
     lat (usec): min=422, max=6603, avg=806.31, stdev=521.10 # 1335.19 -> 1308.42 -> 806.31 ~ 延迟 -40%
    clat percentiles (usec):
     |  1.00th=[  433],  5.00th=[  478], 10.00th=[  494], 20.00th=[  515],
     | 30.00th=[  529], 40.00th=[  545], 50.00th=[  570], 60.00th=[  594],
     | 70.00th=[  685], 80.00th=[  955], 90.00th=[ 1369], 95.00th=[ 1680],
     | 99.00th=[ 3294], 99.50th=[ 3687], 99.90th=[ 4490], 99.95th=[ 4621],
     | 99.99th=[ 5997]
   bw (  KiB/s): min=177152, max=488495, per=99.99%, avg=315045.88, stdev=39879.11, samples=119
   iops        : min=  692, max= 1908, avg=1230.48, stdev=155.80, samples=119
  lat (usec)   : 500=12.73%, 750=59.97%, 1000=10.47%
  lat (msec)   : 2=13.25%, 4=3.30%, 10=0.28%
  cpu          : usr=1.81%, sys=3.91%, ctx=73847, majf=0, minf=74
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=73844,0,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
   READ: bw=308MiB/s (323MB/s), 308MiB/s-308MiB/s (323MB/s-323MB/s), io=18.0GiB (19.4GB), run=60001-60001msec

Disk stats (read/write):
  sdb: ios=73550/0, merge=0/0, ticks=56787/0, in_queue=56787, util=100.00%
```

- 无预读，10GbE nbu nbd 备份 65.9M/s
- 有mmap预读，无预先 vmtouch，10GbE nbu 备份，nbd，69.4M/s +5%
- 有mmap预读，预先 vmtouch，10GbE nbu 备份，nbd，80.9M/s +22.8%
- 对比原生 nfs，10GbE nbu 备份，nbd，

```
$vmtouch ./96b2cdb8-56f4-4cb5.bin
           Files: 1
     Directories: 0
  Resident Pages: 10485760/10485760  40G/40G  100%
         Elapsed: 0.61009 seconds
```
