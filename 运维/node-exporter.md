```bash
docker run -d --name node-exporter --restart=unless-stopped \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host
```

```yaml
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
