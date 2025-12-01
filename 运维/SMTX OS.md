#SMTXOS

# Watchdog 日志

#watchdog

```bash
curl http://127.0.0.1:10300/api/v1/watchdog/log_warn
```

# 单副本

#meta

```bash
echo "DISABLE_SINGLE_REPLICA=false" >> /etc/sysconfig/zbs-metad
systemctl restart zbs-metad

# 1R
zbs-nfs export list | tail -n +3 | awk '{print $2}' | xargs -I{} zbs-nfs export update {} --replica_num 1
echo "1" > /etc/sysconfig/octopus-nfs-replicas
systemctl restart prometheus
rm -rf /usr/share/smartx/images/tmp
zbs-meta pool update default --replica_num 1

# 2R
zbs-nfs export list | tail -n +3 | awk '{print $2}' | xargs -I{} zbs-nfs export update {} --replica_num 2
echo "2" > /etc/sysconfig/octopus-nfs-replicas
systemctl restart prometheus
rm -rf /usr/share/smartx/images/tmp
zbs-meta pool update default --replica_num 2
```

## 重新部署

```bash
rm -f /var/lib/zbs/cluster_uuiœd
rm -f /etc/zbs/.zbsenabled
systemctl restart zbs-deploy-server
```

# 取消资源预留

```
# < 5.0
sed -i 's/DEFAULT_SMARTX_OS_MEMORY = 2\*\*34/DEFAULT_SMARTX_OS_MEMORY = 0/g' /usr/lib/python2.7/site-packages/smartx_app/elf/common/constants.py
systemctl restart elf-vm-scheduler job-center-worker job-center-scheduler zbs-rest-server

# >= 5.0
echo 0 >/etc/zbs/os_memory_used
zbs-node collect_node_info
```

## 新版内存需要 40G 预留，干掉他

```
echo test > /etc/zbs/.deployment_env
```

# meta 配置文件

```bash
# /etc/sysconfig/zbs-metad
base_dir=/var/lib/zbs
meta_root=/var/lib/zbs/metad
foreground=false
leveldb_path=/var/lib/zbs/metad/db/

ZBS_METAD_OPTIONS="-base_dir=/var/lib/zbs -foreground=false -leveldb_path=/var/lib/zbs/metad/db/"
META_ADDR=10.0.20.61
META_PORT=10100
ZK_HOSTS=10.0.20.61:2181,10.0.20.62:2181,10.0.20.63:2181
```

# zbs.conf

```bash
# cat /etc/zbs/zbs.conf
[network]
data_ip = 10.0.20.61
heartbeat_ip = 10.0.20.61
vm_ip = 192.168.20.61
web_ip = 192.168.20.61
access_ip = 172.0.20.61

[cluster]
role = master
members = 10.0.20.61,10.0.20.62,10.0.20.63
zookeeper = 10.0.20.61:2181,10.0.20.62:2181,10.0.20.63:2181
mongo = 10.0.20.61:27017,10.0.20.62:27017,10.0.20.63:27017
cluster_mgt_ips = 192.168.20.61,192.168.20.62,192.168.20.63
cluster_storage_ips = 10.0.20.61,10.0.20.62,10.0.20.63
```

# chunk

```bash
# cat /etc/sysconfig/zbs-chunkd
CHUNK_SERVER_RPC_IP=172.16.30.34
CHUNK_SERVER_RPC_PORT=10200
CHUNK_SERVER_DATA_IP=172.16.30.34
CHUNK_SERVER_DATA_PORT=10201
CHUNK_SERVER_SECONDARY_DATA_IP=192.168.20.34
ZK_HOSTS=172.16.30.31:2181,172.16.30.34:2181
TCMALLOC_MAX_TOTAL_THREAD_CACHE_BYTES=268435456
ACCESS_ENABLE_ISCSI=true
ACCESS_ENABLE_NFS=true
PREFER_LOCAL=true
STORAGE_NIC_BW=1310720000
CHUNK_USE_LSM2=true
```

# 修改 ZooKeeper 内存上限

```bash
nano /etc/systemd/system/smtx-infra-zk.slice.d/50-MemoryLimit.conf
systemctl daemon-reload && systemctl restart smtx-infra-zk.slice
cat /sys/fs/cgroup/memory/smtx.slice/smtx-infra.slice/smtx-infra-zk.slice/memory.limit_in_bytes
nano $(which zkEnv.sh)
```

# 读邮件

```bash
mailx
> seen *
> q
```

# 删除日志收集文件夹的 UUID

```python
# trim uuid
python -c 'import glob, os; a = glob.glob("./*/"); list(map(lambda xy: os.rename(xy[0], xy[1]), filter(lambda xy: xy[1].count("_") == 3, [(x, y[: y.rfind("_")]) for (x, y) in zip(a, a)])))'
```

## 更新 cluster uuid

```
[zk: 10.0.134.194:2181(CONNECTED) 2] set /smx/cfg/meta/cluster_uuid 3bbd7d77-5eca-478d-8409-10fafe2e44e0
```
