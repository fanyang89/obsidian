#prometheus #exporter #监控

# config

```yaml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
#rule_files:
#  - "uptime.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  - job_name: "arc"
    static_configs:
      - targets:
          - "192.168.90.221:9100" # arc
          - "192.168.90.221:9256" # arc
  - job_name: "www2"
    static_configs:
      - targets:
          - "192.168.91.19:9100" # www2 node
          - "192.168.91.19:9256" # www2 process
  - job_name: "www3"
    static_configs:
      - targets:
          - "192.168.31.231:9100" # www3 node
          - "192.168.31.231:9256" # www3 process
  - job_name: "oe1"
    static_configs:
      - targets:
          - "192.168.90.44:9100"
          - "192.168.90.44:9256"
  - job_name: "ci"
    static_configs:
      - targets:
          - "192.168.28.105:9100"
          - "192.168.23.47:9100"
          - "192.168.25.1:9100"
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
```

# node-exporter

```yaml
# cat docker-compose.yml
---
version: "3.8"

services:
  node_exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node-exporter
    command:
      - "--path.rootfs=/host"
    network_mode: host
    pid: host
    restart: unless-stopped
    volumes:
      - "/:/host:ro,rslave"
```

```bash
docker run --name node-exporter \
-d --net host --pid host \
--restart=unless-stopped \
-v /:/host:ro quay.io/prometheus/node-exporter:latest \
--path.rootfs=/host
```

# process-exporter

```yaml
# mkdir -p ~/app/process-exporter/config
# nano ~/app/process-exporter/config/config.yaml
process_names:
  - name: "{{.Comm}}"
    cmdline:
      - ".+"
```

```bash
#!/usr/bin/env bash

docker run --name process-exporter \
-d -p 9256:9256 --privileged \
--restart=unless-stopped \
-v /proc:/host/proc \
-v "$HOME/app/process-exporter/config:/config" ncabatoff/process-exporter \
--procfs /host/proc \
-config.path /config/config.yaml
```

# 删除某个 series

```bash
curl -X POST -g 'http://127.0.0.1:9090/api/v1/admin/tsdb/delete_series?match[]={job="infra"}'
```
