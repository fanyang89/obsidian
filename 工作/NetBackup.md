#NBU

设置NET_BUFFER_SZ ：
在应用Netbackup备份数据到带库时，有一个NET_BUFFER_SZ参数，决定media server与client之间数据传输的缓冲池大小，该参数值默认为32032 bytes，以文件形式保存于%Install_Path/netbackup/目录下。通常默认值都较小，如果希望修改该参数值，则直接修改%Install_Path/netbackup/NET_BUFFER_SZ即可，例如：

host-192-168-241-40:/usr/openv/netbackup # echo 65536 > /usr/openv/netbackup/NET_BUFFER_SZ  
Host-192-168-241-40:/usr/openv/netbackup # more /usr/openv/netbackup/NET_BUFFER_SZ 65536

### 4、超时设置

超时属性适用于选定的Master server、Media Server以及NBU client

- **Client connect timeout：**

​ 此选项指定服务器连接客户端时等待的秒数。默认值为300s。一般Roach备份业务中，建议此值设置为3600s或者更高。此选项适用于NBU Master Server、NBU Media Server、NBU Client。

- **Client read timeout：**

​ 此选项指定用于客户端读取超时的秒数。此选项适用于NBU Master Server、NBU Media Server。默认值为300s，如果服务器在客户端在此超时时间内没有从客户端得到响应，则备份/恢复任务失败，报错误码13。特别是针对于NBU Job复用场景，文件间隔传输时间超过此值，则备份/恢复任务失败。建议此值设置为3600s或者更高。

- **Media server connect timeout**

​ 此选项用于指定 Master Server 连接 Media Server 的等待超时描述，默认值为 300s。建议此值设置为 3600s。此选项适用于 NBU Master Server、NBU Media Server。

https://segmentfault.com/a/1190000038510005

2、设置Data Buffer Size和Number of Data Buffers  
SIZE_DATA_BUFFERS：用于指定bptm/bpdm进程间通信的缓存大小，同时也是bptm进程写磁带时的块大小，默认大小是32K，有效值为32-256K(必须是1024b的整倍数)。  
NUM_DATA_BUFFERS：用于指定bptp进程可用的Data_buffer数，默认大小是8，有效值为4-16。  
这两个参数均位于%Install_Path/netbackup/db/config目录下，以同名文件形式存在，如果不存在，说明当前仅使用了默认值，要修改该参数值，只需创建(或修改)同名文件即可，例如，设置data_buffer为256K，设置buffer number为16个：  
[root@newtrade2](mailto:root@newtrade2) # echo 262144 > /usr/openv/netbackup/db/config/SIZE_DATA_BUFFERS  
[root@newtrade2](mailto:root@newtrade2) # echo 16 > /usr/openv/netbackup/db/config/NUMBER_DATA_BUFFERS  
[root@newtrade2](mailto:root@newtrade2) # more /usr/openv/netbackup/db/config/SIZE_DATA_BUFFERS  
262144  
[root@newtrade2](mailto:root@newtrade2) # more /usr/openv/netbackup/db/config/NUMBER_DATA_BUFFERS  
16

不过该参数并非越大越好，我们都知道任何设备都是有极限的，因此一旦你的设置超出了设备的承受能力，则有可能适得其反。最常见的情况是设置完 size_data_buffers 和 number_data_buffers 后，备份效率大大提升，但恢复速度极具下降，甚至恢复出错。因此，上述两参数如果发生修改，强烈建议务必进行备份和恢复测试，包括对修改前的备份做恢复和修改后的备份做恢复测试。

[root@nbumedia2 ~]# hostname -I
192.168.30.178 172.16.30.178 192.168.122.1

如果还是慢怎么办
尝试更换 vCenter
尝试更换 vCenter 为更高版本。因为实际上瓶颈往往在于 ESXi/vCenter 的 NFC（Network File Copy Server）。万一 VMware 突然更新了解决了性能问题呢？这属于死马当活马医的方法一，没准你就瞎猫撞上死老鼠好使了。Veeam 论坛有讨论该问题（Veeam 用 ESXi 7.0 的时候备份比牛车还慢，参见售后系统 喜士多广州集群SMTX OS + Veeam 备份恢复速度只有3M）

设置一些参数
下面这些命令是从华为的文档里抄来的。根据测试，这些参数多数情况下并没有什么用（默认值已经还能用了）。
在 NetBackup Master Server 和 Media Server 处运行如下命令
echo 65536 > /usr/openv/netbackup/NET_BUFFER_SZ
echo 262144 > /usr/openv/netbackup/db/config/SIZE_DATA_BUFFERS
echo 16 > /usr/openv/netbackup/db/config/NUMBER_DATA_BUFFERS

运行上述命令后，需要重启 Server。
/usr/openv/netbackup/bin/bp.kill_all
/usr/openv/netbackup/bin/bp.start_all

尝试重新安装 Media Server
测试过程中出现过无法开启 HotAdd 的情况。重新安装 Media Server 后可以正常开启 HotAdd。具体原因不明。
