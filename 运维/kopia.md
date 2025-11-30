---
title: kopia
---

# kopia

```bash
kopia repository connect webdav --url http://10.1.2.5:5005/backup/KRepo/backup
kopia repository connect webdav --url http://10.1.2.5:5005/backup/KRepo/mbp
kopia repository connect webdav --url http://10.1.2.5:5005/backup/KRepo/devkits

cp ~/.config/kopia/repository.config repository.config

# edit cache path
vim repository.config
kopia snapshot migrate --source-config ./repository.config --all
```

```bash
kopia repository create s3 --endpoint=oss-us-west-1.aliyuncs.com --bucket=fanmi-backup-us --access-key=LTAI5tMhfp1arTU8m9W5tF8p --secret-access-key=WhfdY5eZ5LvvfXlwt3L6l7VkjDFTuI --ecc-overhead-percent=10
```

```bash
-> % cat /usr/lib/systemd/system/kopia-snapshot.timer;
[Unit]
Description=kopia snapshot timer

[Timer]
OnBootSec=1h
OnUnitActiveSec=8h
Unit=kopia-snapshot.service

[Install]
WantedBy=multi-user.target

fanmi@www [15:03:04] [~]
-> % cat /usr/lib/systemd/system/kopia-snapshot.service
[Unit]
Description=kopia snapshot

[Service]
ExecStart=/usr/bin/kopia snapshot create /mnt/uofs2
User=fanmi
Group=fanmi
```

## 配置文件位置

By default, the configuration file is located in your home directory under:

- `%APPDATA%\kopia\repository.config` on Windows
- `$HOME/Library/Application Support/kopia/repository.config` on macOS
- `$HOME/.config/kopia/repository.config` on Linux
