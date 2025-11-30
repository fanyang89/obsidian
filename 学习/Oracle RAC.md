#oracle

```
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_1of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_1of7.zip
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_2of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_2of7.zip
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_3of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_3of7.zip
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_4of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_4of7.zip
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_5of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_5of7.zip
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_6of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_6of7.zip
https://updates.oracle.com/Orion/Services/download/p13390677_112040_Linux-x86-64_7of7.zip?aru=16716375&patch_file=p13390677_112040_Linux-x86-64_7of7.zip
```

![](assets/Pasted%20image%2020230825163102.png)

![](assets/Pasted%20image%2020230825163122.png)

![](assets/Pasted%20image%2020230825163147.png)

![](assets/Pasted%20image%2020230825163549.png)

![](assets/Pasted%20image%2020230825163606.png)

![](assets/Pasted%20image%2020230825164305.png)

![](assets/Pasted%20image%2020230825164502.png)

![](assets/Pasted%20image%2020230825165159.png)

![](assets/Pasted%20image%2020230825165754.png)

![](assets/Pasted%20image%2020230825165904.png)

![](assets/Pasted%20image%2020230825170150.png)

![](assets/Pasted%20image%2020230825170748.png)

![](assets/Pasted%20image%2020230825171658.png)

![](assets/Pasted%20image%2020230825172105.png)

![](assets/Pasted%20image%2020230825172313.png)

![](assets/Pasted%20image%2020230825172353.png)

![](assets/Pasted%20image%2020230825172920.png)

![](assets/Pasted%20image%2020230825175526.png)

![](assets/Pasted%20image%2020230825180034.png)

```bash
uptime
blockdev --setro /dev/sdd
blockdev --setro /dev/sdb
blockdev --setro /dev/sdc
sleep 202s
blockdev --setrw /dev/sdd
blockdev --setrw /dev/sdb
blockdev --setrw /dev/sdc
uptime
```

```bash
[root@rac2 ~]# uptime
 18:41:20 up  2:55,  1 user,  load average: 0.00, 0.00, 0.00
[root@rac2 ~]# blockdev --setro /dev/sdd
[root@rac2 ~]# blockdev --setro /dev/sdb
[root@rac2 ~]# blockdev --setro /dev/sdc
[root@rac2 ~]# sleep 22s
[root@rac2 ~]# blockdev --setrw /dev/sdd
[root@rac2 ~]# blockdev --setrw /dev/sdb
[root@rac2 ~]# blockdev --setrw /dev/sdc
[root@rac2 ~]# blockdev --setrw /dev/sdd^C
[root@rac2 ~]# uptime
 18:42:05 up  2:56,  1 user,  load average: 0.00, 0.00, 0.00

# 32s ok
# 202s ok
```

```bash
uptime
nmcli c down ens225
sleep 32s
nmcli c up ens225
date
uptime
# ok

uptime
nmcli c down ens225
sleep 62s
nmcli c up ens225
date
uptime
# ok
```

```
root@node21 19:36:27 ~]$zbs-meta volume listall | grep 'datastore-r2' | grep 64.00 | grep 2023-08-28
26423e00-9138-47cb-9ff3-b3bf490cdd7c  26423e00-9138-47cb                    336cef4d-c29f-4b48-a60d-4be94f2af7f3  datastore-r2                                                        64.00 GiB    31.75 GiB      0.00 B         2023-08-28 19:02:19.842134228  VOLUME_ONLINE                 2  True              False                       False                     0             0
880a89ab-06f5-41c5-a7e2-3f0cfd1f903a  880a89ab-06f5-41c5                    336cef4d-c29f-4b48-a60d-4be94f2af7f3  datastore-r2                                                        64.00 GiB    30.75 GiB      0.00 B         2023-08-28 18:59:30.791373847  VOLUME_ONLINE                 2  True              False                       False                     0             0
```

```
$zbs-nfs inode show 26423e00-9138-47cb
-----------  ------------------------------------
id           26423e00-9138-47cb
name         fanyang-rac2-flat.vmdk
pool         336cef4d-c29f-4b48-a60d-4be94f2af7f3
preallocate  False
volume       26423e00-9138-47cb-9ff3-b3bf490cdd7c
type         FILE
mode         384
uid          0
gid          0
size         64.00 GiB
unique size  31.75 GiB
shared size  0.00 B
-----------  ------------------------------------
```

![](assets/Pasted%20image%2020230919183515.png)

```
[grid@rac6 ~]$ sqlplus / as sysdba
```
