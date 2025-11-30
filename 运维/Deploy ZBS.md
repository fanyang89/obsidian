#zbs

# zbs-metad

```bash
# cat /etc/sysconfig/zbs-metad
ZBS_METAD_OPTIONS="-base_dir=/var/lib/zbs -leveldb_path=/var/lib/zbs/metad/db"
META_ADDR=127.0.0.1
META_PORT=10100
ZK_HOSTS=127.0.0.1:2181
```

```bash
# /usr/lib/systemd/system/zbs-metad.service
[Unit]
Description=ZBS meta service
After=zookeeper.service

[Service]
Type=simple
EnvironmentFile=/etc/sysconfig/zbs-metad
ExecStart=/usr/local/bin/zbs-metad
Restart=on-failure
RestartSec=1s
User=root
Group=root

[Install]
WantedBy=multi-user.target
```
