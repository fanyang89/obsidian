---
title: 分发 RCLONE 配置文件
---

# 分发 RCLONE 配置文件

#rclone

```bash
# dist-rclone-config.sh
#!/usr/bin/env bash

set -xeuo pipefail

sshpass -p Fanmi.5589 ssh -o StrictHostKeyChecking=no root@192.168.91.11   'bash -s' < "$HOME"/update-rclone-config.sh
sshpass -p Fanmi.5589 ssh -o StrictHostKeyChecking=no root@192.168.91.19   'bash -s' < "$HOME"/update-rclone-config.sh
sshpass -p fanmi      ssh -o StrictHostKeyChecking=no fanmi@192.168.91.19  'bash -s' < "$HOME"/update-rclone-config.sh
sshpass -p fanmi      ssh -o StrictHostKeyChecking=no fanmi@192.168.126.88 'bash -s' < "$HOME"/update-rclone-config.sh
```

```bash
# cat ./publish-rclone-config.sh
#!/usr/bin/env bash

set -xeuo pipefail

LOCAL_CONFIG="$(rclone config file 2>&1 | tail -n1)"
rsync -avzP --chmod=F644 "$LOCAL_CONFIG" root@192.168.91.19:/wwwroot/archive/rclone.conf
```

```bash
# cat update-rclone-config.sh
#!/usr/bin/env bash

set -xeuo pipefail

REMOTE_CONF="http://192.168.91.19/archive/rclone.conf"
LOCAL_CONFIG="$(rclone config file 2>&1 | tail -n1)"

curl -L "$REMOTE_CONF" > "$LOCAL_CONFIG"
```
