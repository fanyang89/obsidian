#clash

```bash
#!/usr/bin/env bash

set -xeuo pipefail

# params
CLASH_CONFIG="http://192.168.31.215/smartx.yaml"
CLASH_CONFIG_OUTPUT="$PWD/smartx-rules.yaml"
OUTPUT_DIR="$PWD/Rules"
PROXY="http://192.168.126.1:7890"
PREFIX="https://dler.cloud/Rules/Clash/Provider/"

# download rules yaml
YAML_URL=($(curl -s "$CLASH_CONFIG" | grep 'url:' | grep 'yaml$' | awk '{print $2}'))
mkdir -p "$OUTPUT_DIR"

for ((idx=0; idx<${#YAML_URL[@]}; ++idx)); do
	URL="${YAML_URL[idx]}"
	OUTPUT=$(echo "$URL" | grep -oP "^$PREFIX\K.*")
	OUTPUT_BASE=$(dirname "$OUTPUT")
	mkdir -p "$OUTPUT_DIR/$OUTPUT_BASE"
	curl -s -x "$PROXY" -L "$URL" > "$OUTPUT_DIR/$OUTPUT"
done

# modify smartx.yaml
curl -s "$CLASH_CONFIG" | sed 's/url: https:\/\/dler.cloud\/Rules\/Clash\/Provider/url: http:\/\/192.168.31.215\/Rules/g' > "$CLASH_CONFIG_OUTPUT"
```

## 定时更新订阅

```bash
# cat /etc/systemd/system/update-clash-config.service
[Unit]
Description=Update slack status

[Service]
ExecStart=/bin/bash /etc/clash-meta/update.sh
WorkingDirectory=/etc/clash-meta
User=clash-meta
Group=clash-meta
```

```bash
# cat /etc/systemd/system/update-clash-config.timer
[Unit]
Description=Update clash-meta config

[Timer]
OnBootSec=15min
OnUnitActiveSec=1d
Persistent=true

[Install]
WantedBy=timers.target
```
