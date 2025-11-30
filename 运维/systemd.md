# Timer

```bash
-> % cat /etc/systemd/system/meilidex.timer;
[Unit]
Description=Update meilisearch index

[Timer]
OnBootSec=10min
OnUnitActiveSec=13min

[Install]
WantedBy=timers.target
```

```bash
-> % cat /etc/systemd/system/meilidex.service;
[Unit]
Description=Update meilisearch index

[Service]
ExecStart=/bin/bash /mnt/uofs2/scripts/meilidex/sync-index.sh
WorkingDirectory=/mnt/uofs2/scripts/meilidex
User=fanmi
Group=fanmi
```

```
-> % cat /etc/systemd/system/update-slack-status.service
[Unit]
Description=Update slack status

[Service]
ExecStart=/bin/bash /mnt/uofs2/scripts/slack-status/update.sh
WorkingDirectory=/mnt/uofs2/scripts/slack-status

User=fanmi
Group=fanmi
```
