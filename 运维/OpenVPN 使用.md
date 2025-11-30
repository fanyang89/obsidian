---
title: OpenVPN 使用
---

# OpenVPN 使用

```
openvpn3 config-import --config ./client.conf --name client
openvpn3 config-manage --config client --allow-compression yes
systemctl enable --now openvpn3-session@client
openvpn3 session-manage --disconnect --config client
```
