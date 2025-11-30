#oracle

# 配置

虚拟机两台。

- 发行版：OracleLinux-R7-U9-Server-x86_64-dvd.iso
  - 安装时选择 Server with GUI
- 8 CPUs
- 16G RAM
- 网卡
  - Public NIC（承载 SCAN，VIP）
  - Private NIC
- 磁盘 - 64G 系统盘 - 2G \* 3 - 20G - 40G
  除了系统盘，其余五个盘是共享

# 节点信息

| hostname     | Public IP        | Private IP        |
| ------------ | ---------------- | ----------------- |
| fanyang-rac3 | 192.168.31.63/20 | 10.100.100.103/24 |
| fanyang-rac4 | 192.168.27.25/20 | 10.100.100.104/24 |

## 检查环境

```bash
yum install -y oracle-database-server-12cR2-preinstall
/usr/bin/oracle-database-server-12cR2-preinstall-verify
```

## 安装依赖

```bash
export LANG=C
rpm -q --queryformat "%{NAME}-%{VERSION}-%{RELEASE} (%{ARCH})\n" \ bc compat-libcap1* compat-libcap* binutils compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel gcc gcc-c++ glibc-2.5 glibc-common glibc-devel glibc-headers ksh libaio libaio-devel libgcc libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel binutils* compat-libstdc* elfutils-libelf* glibc* ksh* libaio* libgcc* libstdc* sysstat* libXp* glibc-kernheaders net-tools-* | grep not | awk '{print $2}' | tr '\n' ' '

yum install -y bc compat-libcap1* compat-libcap* compat-libstdc++-33 elfutils-libelf-devel gcc gcc-c++ glibc-2.5 glibc-devel glibc-headers ksh libaio-devel libstdc++-devel unixODBC unixODBC-devel binutils* compat-libstdc* elfutils-libelf* glibc* ksh* libaio* libgcc* libstdc* sysstat* libXp* glibc-kernheaders
```

```
groupadd oinstall
groupadd dba
groupadd oper
useradd -g oinstall -G dba,oper oracle

mkdir -p /u01/app/oracle
mkdir -p /u01/oraInventory
chown -R oracle.oinstall /u01
chown -R oracle:oinstall /u01/app/oracle
chmod -R 775 /u01/app/oracle

passwd oracle
id oracle
```

```bash
yum install -y oracleasm-support
```

```
INFO: /usr/bin/ld: /u01/app/12.1.0/grid/lib//libnls12.a(lxhlang.o): undefined reference to symbol '__tls_get_addr@@GLIBC_2.3'
//usr/lib64/ld-linux-x86-64.so.2: error adding symbols: DSO missing from command line
```

```
ls /u01/app/12.1.0/grid/lib/stubs

1) backup "$ORACLE_HOME/lib/stubs" and then delete it.
vim "$ORACLE_HOME/rdbms/lib/env_rdbms.mk"
RMAN_LINKLINE=$(LINK) $(OPT) $(S0MAIN) $(SSKRMED) $(SKRMPT) \
        $(LLIBDBTOOLS) $(LLIBCLIENT) $(LLIBSQL) $(LLIBPLSQL) \
        $(LLIBSNLSRTL) $(LLIBUNLSRTL) $(LLIBNLSRTL) \
        $(LLIBSLAX) $(LLIBPLSQL) $(LIBPLCN) $(LINKTTLIBS) -lons   <----
```

```
INFO: /usr/bin/ld: /u01/app/12.1.0/grid/lib//libclient12.a(kpue.o): undefined reference to symbol 'ons_subscriber_close'
/u01/app/12.1.0/grid/lib/libons.so: error adding symbols: DSO missing from command line

INFO: collect2: error: ld returned 1 exit status
INFO: make: *** [/u01/app/12.1.0/grid/rdbms/lib/plshprof] Error 1
```

```
su -l grid
vim "$ORACLE_HOME/rdbms/lib/env_rdbms.mk"

PLSHPROF_LINKLINE=$(LINK) $(OPT) $(PLSTMAI) $(SSDBED) \
        $(LLIBCLIENT) $(LLIBPLSQL) $(LLIBSLAX) $(LLIBPLSQL) $(LINKTTLIBS) -lons
RMAN_LINKLINE=$(LINK) $(OPT) $(S0MAIN) $(SSKRMED) $(SKRMPT) \
        $(LLIBDBTOOLS) $(LLIBCLIENT) $(LLIBSQL) $(LLIBPLSQL) \
        $(LLIBSNLSRTL) $(LLIBUNLSRTL) $(LLIBNLSRTL) \
        $(LLIBSLAX) $(LLIBPLSQL) $(LIBPLCN) $(LINKTTLIBS) -lons
TG4PWD_LINKLINE= $(LINK) $(OPT) $(TG4PWDMAI) \
        $(LLIBTHREAD) $(LLIBCLNTSH) $(LINKLDLIBS) -lnnz12
```

```
INFO: ERROR:
INFO: PRVG-1101 : SCAN name "scan" failed to resolve
INFO: ERROR:
INFO: PRVF-4657 : Name resolution setup check for "scan" (IP address: 192.168.31.69) failed
INFO: ERROR:
INFO: PRVF-4664 : Found inconsistent name resolution entries for SCAN name "scan"
```

```
srvctl relocate vip -vip rac4-vip -node rac4
```

# Day2

ip 规划：public IP 通，private IP 通，vip scan 不通（Oracle 会自动配置）

```bash
yum update -y; reboot
```

```
# vim /etc/yum.repos.d/oracle-linux-ol7.repo
[ol7_ociyum_config]
name=OCI specific release packages Oracle Linux $releasever ($basearch)
baseurl=https://yum$ociregion.oracle.com/repo/OracleLinux/OL7/oci/included/x86_64/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0

# yum install -y oracle-rdbms-server-12cR1-preinstall
# oracle-rdbms-server-12cR1-preinstall-verify; echo $?

# systemctl disable --now firewalld
# setenforce 0
# vim /etc/selinux/config
SELINUX=disabled

vim /etc/security/limits.conf
oracle   soft   nofile    1024
oracle   hard   nofile    65536
oracle   soft   nproc    2047
oracle   hard   nproc    16384
oracle   soft   stack    10240
oracle   hard   stack    32768

vim /etc/security/limits.d/90-nproc.conf
*          -       nproc     16384
root       soft    nproc     unlimited

yum install binutils compat-libcap1 compat-libstdc++-33 compat-libstdc++-33.i686 gcc gcc-c++ glibc glibc.i686 glibc-devel glibc-devel.i686 ksh libgcc libgcc.i686 libstdc++ libstdc++.i686 libstdc++-devel libstdc++-devel.i686 libaio libaio.i686 libaio-devel libaio-devel.i686 libXext libXext.i686 libXtst libXtst.i686 libX11 libX11.i686 libXau libXau.i686 libxcb libxcb.i686 libXi libXi.i686 make sysstat unixODBC unixODBC-devel

rpm -q binutils compat-libcap1 compat-libstdc++-33 compat-libstdc++-33.i686 gcc gcc-c++ glibc glibc.i686 glibc-devel glibc-devel.i686 ksh libgcc libgcc.i686 libstdc++ libstdc++.i686 libstdc++-devel libstdc++-devel.i686 libaio libaio.i686 libaio-devel libaio-devel.i686 libXext libXext.i686 libXtst libXtst.i686 libX11 libX11.i686 libXau libXau.i686 libxcb libxcb.i686 libXi libXi.i686 make sysstat unixODBC unixODBC-devel | grep not
```

```
groupadd -g 1000 oinstall
groupadd -g 1010 dba
groupadd -g 1020 asmadmin
groupadd -g 1030 asmdba
useradd -u 1101 -g oinstall -G dba,asmdba oracle
mkdir -p /u01
chmod -R 775 /u01
passwd oracle

useradd -u 1100 -g oinstall -G asmadmin,asmdba grid
chown -R grid:oinstall /u01
passwd grid

mkdir -p /u01/app/12.1.0/grid
mkdir -p /u01/app/grid
mkdir -p /u01/app/oracle/product/12.1.0
chown -R oracle:oinstall /u01/app/
chown -R grid:oinstall /u01/app/
chown -R grid:oinstall /u01/app/12.1.0
chown -R grid:oinstall /u01/app/12.1.0/grid
chown -R grid:oinstall /u01/app/grid/

su -l grid
vim ~/.bashrc

export ORACLE_HOME=/u01/app/12.1.0/grid
export ORACLE_SID=+ASM1  # Node1 -> ASM1, Node2 -> ASM2
export PATH=$ORACLE_HOME/bin:$PATH
export ORACLE_BASE=/u01/app/grid
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib

su -l oracle
vim ~/.bashrc

export ORACLE_HOME=/u01/app/oracle/product/12.1.0
export ORACLE_SID=vstracdb1
export PATH=$ORACLE_HOME/bin:$PATH
export ORACLE_BASE=/u01/app/oracle
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
```

```
vim /etc/hosts
rac5 192.168.31.19
rac5-vip 192.168.31.17
rac5-p   10.100.100.105

rac6 192.168.31.20
rac6-vip 192.168.31.14
rac6-p   10.100.100.106

scan1 192.168.xx.xx
```

# 创建共享盘

- 2G
- 2G
- 2G
- 20G
- 40G

```
# yum install -y oracleasm-support

# oracleasm configure -i
Default user to own the driver interface []: grid
Default group to own the driver interface []: asmadmin
Start Oracle ASM library driver on boot (y/n) [n]: y
Scan for Oracle ASM disks on boot (y/n) [y]: y

parted /dev/sdb mklabel gpt
parted /dev/sdc mklabel gpt
parted /dev/sdd mklabel gpt
parted /dev/sde mklabel gpt
parted /dev/sdf mklabel gpt

parted /dev/sdb mkpart a ext4 0% 100%
parted /dev/sdc mkpart a ext4 0% 100%
parted /dev/sdd mkpart a ext4 0% 100%
parted /dev/sde mkpart a ext4 0% 100%
parted /dev/sdf mkpart a ext4 0% 100%

oracleasm init
oracleasm createdisk disk1 /dev/sdb1
oracleasm createdisk disk2 /dev/sdc1
oracleasm createdisk disk3 /dev/sdd1
oracleasm createdisk disk4 /dev/sde1
oracleasm createdisk disk5 /dev/sdf1

oracleasm scandisks
oracleasm listdisks
```

```
[root@rac5 ~]# rpm -Uvh /home/grid/V38501/grid/rpm/cvuqdisk-1.0.9-1.rpm
```

VNC 登录 grid 用户，运行：

```
# pwd
/home/grid/V38501/grid

# ./runInstaller -jreLoc /etc/alternatives/jre_1.8.0
```

![](assets/Pasted%20image%2020230905130551.png)

![](assets/Pasted%20image%2020230905130610.png)
yes

![](assets/Pasted%20image%2020230905130628.png)

![](assets/Pasted%20image%2020230905130642.png)

![](assets/Pasted%20image%2020230905130655.png)

![](assets/Pasted%20image%2020230905130724.png)

![](assets/Pasted%20image%2020230905130738.png)

![](assets/Pasted%20image%2020230905130751.png)
Setup、Test

![](assets/Pasted%20image%2020230905130821.png)

![](assets/Pasted%20image%2020230905130834.png)

![](assets/Pasted%20image%2020230905131104.png)
遇到此错误，请检查 SELinux 和防火墙是否已经关闭，Public IP、Private IP 是否正确，子网掩码是否正确。

![](assets/Pasted%20image%2020230905132936.png)

```
[root@rac5 disks]# ls -lah
总用量 0
drwxr-xr-x. 1 root root         0 9月   5 11:41 .
drwxr-xr-x. 4 root root         0 9月   5 11:41 ..
brw-rw----. 1 grid asmadmin 8, 81 9月   5 11:41 DISK1
brw-rw----. 1 grid asmadmin 8, 17 9月   5 11:41 DISK2
brw-rw----. 1 grid asmadmin 8, 33 9月   5 11:41 DISK3
brw-rw----. 1 grid asmadmin 8, 49 9月   5 11:41 DISK4
brw-rw----. 1 grid asmadmin 8, 65 9月   5 11:41 DISK5
```

![](assets/Pasted%20image%2020230905133159.png)

![](assets/Pasted%20image%2020230905133214.png)

![](assets/Pasted%20image%2020230905133340.png)

![](assets/Pasted%20image%2020230905133354.png)

![](assets/Pasted%20image%2020230905133416.png)

![](assets/Pasted%20image%2020230905133428.png)

![](assets/Pasted%20image%2020230905133539.png)

```
su -l grid
vim "$ORACLE_HOME/rdbms/lib/env_rdbms.mk"

PLSHPROF_LINKLINE=$(LINK) $(OPT) $(PLSTMAI) $(SSDBED) \
        $(LLIBCLIENT) $(LLIBPLSQL) $(LLIBSLAX) $(LLIBPLSQL) $(LINKTTLIBS) -lons  <--- 增加 -lons

RMAN_LINKLINE=$(LINK) $(OPT) $(S0MAIN) $(SSKRMED) $(SKRMPT) \
        $(LLIBDBTOOLS) $(LLIBCLIENT) $(LLIBSQL) $(LLIBPLSQL) \
        $(LLIBSNLSRTL) $(LLIBUNLSRTL) $(LLIBNLSRTL) \
        $(LLIBSLAX) $(LLIBPLSQL) $(LIBPLCN) $(LINKTTLIBS) -lons   <--- 增加 -l

TG4PWD_LINKLINE= $(LINK) $(OPT) $(TG4PWDMAI) \
        $(LLIBTHREAD) $(LLIBCLNTSH) $(LINKLDLIBS) -lnnz12   <--- 增加 -l

mv /u01/app/12.1.0/grid/lib/stubs /u01/app/12.1.0/grid/lib/stubs-bak
```

```
[grid@rac5 grid]$ ./runcluvfy.sh comp nodecon -i eth0 -n rac5,rac6 -verbose
```

```
Check: TCP connectivity of subnet "192.168.16.0"
  Source                          Destination                     Connected?
  ------------------------------  ------------------------------  ----------------
  rac6:192.168.31.23              rac5:192.168.31.19              failed

ERROR:
PRVF-7617 : Node connectivity between "rac6 : 192.168.31.23" and "rac5 : 192.168.31.19" failed
```

添加了两个网卡，是 EverRoute 默认规则导致不通。

![](assets/Pasted%20image%2020230905163749.png)

![](assets/Pasted%20image%2020230905164058.png)

![](assets/Pasted%20image%2020230905164159.png)

![](assets/Pasted%20image%2020230905165742.png)

```
cat >>/usr/lib/systemd/system/ohas.service <<EOF
[Unit]
Description=Oracle High Availability Services
After=syslog.target

[Service]
ExecStart=/etc/init.d/init.ohasd run >/dev/null 2>&1 Type=simple
Restart=always

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable --now ohas.service
systemctl status ohas.service
```

![](assets/Pasted%20image%2020230905170101.png)

![](assets/Pasted%20image%2020230905170654.png)

![](assets/Pasted%20image%2020230905170852.png)

```
[grid@rac5 ~]$ crsctl stat res -t
--------------------------------------------------------------------------------
Name           Target  State        Server                   State details
--------------------------------------------------------------------------------
Local Resources
--------------------------------------------------------------------------------
ora.DATA.dg
               ONLINE  ONLINE       rac5                     STABLE
               ONLINE  ONLINE       rac6                     STABLE
ora.LISTENER.lsnr
               ONLINE  ONLINE       rac5                     STABLE
               ONLINE  ONLINE       rac6                     STABLE
ora.asm
               ONLINE  ONLINE       rac5                     Started,STABLE
               ONLINE  ONLINE       rac6                     Started,STABLE
ora.net1.network
               ONLINE  ONLINE       rac5                     STABLE
               ONLINE  ONLINE       rac6                     STABLE
ora.ons
               ONLINE  ONLINE       rac5                     STABLE
               ONLINE  ONLINE       rac6                     STABLE
--------------------------------------------------------------------------------
Cluster Resources
--------------------------------------------------------------------------------
ora.LISTENER_SCAN1.lsnr
      1        ONLINE  ONLINE       rac5                     STABLE
ora.cvu
      1        ONLINE  ONLINE       rac5                     STABLE
ora.oc4j
      1        OFFLINE OFFLINE                               STABLE
ora.rac5.vip
      1        ONLINE  ONLINE       rac5                     STABLE
ora.rac6.vip
      1        ONLINE  ONLINE       rac6                     STABLE
ora.scan1.vip
      1        ONLINE  ONLINE       rac5                     STABLE
--------------------------------------------------------------------------------
```

其中 `ora.oc4j` 处于 OFFLINE 是正常现象，可按需开启。原因：
[https://forums.oracle.com/ords/apexds/post/what-is-ora-oc4j-service-why-does-my-ora-oc4j-service-was-o-9793](https://forums.oracle.com/ords/apexds/post/what-is-ora-oc4j-service-why-does-my-ora-oc4j-service-was-o-9793)

> The ora.oc4j is for the QoS (Quality of Service Management), which is only available on Exadata anyway. If you installed 11.2.0.1 the OFFLINE OFFLINE is expected and even though with 11.2.0.2 it should be ONLINE you don't worry about ora.oc4j.

> Oracle Exadata 数据库机是为运行 Oracle 数据库而优化的计算平台。Exadata 是硬件和软件的组合平台，包括横向扩展的 x86-64 计算和存储服务器，RoCE 网络，RDMA 寻址内存加速，NVMe 闪存和专用软件。
