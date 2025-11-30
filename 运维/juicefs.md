```bash
# juicefs format --storage minio --bucket http://10.1.2.3:9000/juicefs --access-key Activate2211 --secret-key 'SVp!m@SQU*#Rp8' 'postgres://activate2211:SVp%21m%40SQU%2A%23Rp8@10.1.2.3:5432/juicefs' juicefs
```

```
juicefs format --storage=oss --bucket fanmi-clouddrive.oss-cn-shanghai.aliyuncs.com --access-key LTAI5tL74WREEArVLdxrtt1S --secret-key EZp8DE42H7ZjrPNhp90lmxMuWVnyY5 "redis://:ESxRKM49FbW8v3yh@47.103.35.100:16379" clouddrive

juicefs mount -d --writeback --buffer-size 1000 "redis://:ESxRKM49FbW8v3yh@47.103.35.100:16379" ~/mnt/CloudDrive
```

```sql
mysql> CREATE DATABASE uofs;
mysql> ALTER DATABASE uofs CHARACTER SET utf8mb4;
mysql> ALTER DATABASE uofs CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

CREATE USER 'jfs'@'%' IDENTIFIED BY 'Tw8#7Req2hZ!t4';
GRANT ALL PRIVILEGES ON uofs.* TO 'jfs'@'%';
```

```bash
# format
META_PASSWORD='Tw8#7Req2hZ!t4' juicefs format --force --storage minio --access-key "eoP10yTh3DzgIWk7iZiH" --secret-key "4yO0LRemLApUajXYFyt8IQzDviKVPc7BECOexqUn" --bucket "http://192.168.90.210:9000/uofs" "mysql://jfs@(127.0.0.1:3306)/uofs" uofs

# mount
sudo env META_PASSWORD="DFCGVHNETUkvgHVehVbcEtkFJiuBDKvTJnrHbg" juicefs mount -d "mysql://jfs@(127.0.0.1:3306)/ufs" /mnt/ufs
sudo env META_PASSWORD="DFCGVHNETUkvgHVehVbcEtkFJiuBDKvTJnrHbg" juicefs mount -d "mysql://jfs@(192.168.91.19:3306)/ufs" /mnt/ufs
env META_PASSWORD='Tw8#7Req2hZ!t4' juicefs mount --update-fstab -d "mysql://jfs@(192.168.90.210:3306)/uofs" /mnt/uofs
```

```
2024-02-17T16:19:25.490299Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: ;Uotdj6=:v3T
```

```
-> % cat /etc/systemd/system/mount-uofs.service;
[Unit]
Description=Mount uofs on boot
After=network-online.target auditd.service

[Service]
Type=simple
EnvironmentFile=/etc/juicefs/uofs
ExecStart=/usr/local/bin/juicefs mount "mysql://jfs@(192.168.90.210:3306)/uofs?timeout=5s" /mnt/uofs


[Install]
WantedBy=multi-user.target


-> % cat /etc/juicefs/uofs
META_PASSWORD='Tw8#7Req2hZ!t4'
```

```
REDIS_PASSWORD='Tw8#7Req2hZ!t4' juicefs mount -d "redis://127.0.0.1:6379/0" /mnt/uofs2
```
