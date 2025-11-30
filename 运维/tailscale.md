```bash
TS_AUTHKEY=tskey-auth-kS7kHDHM2r11CNTRL-DJVGbGN7GsgBzPexU3MJpgwj3NoTuMTL
TS_HOSTNAME=t2
network=host
mount /var/lib
```

```
# cat /etc/systemd/system/derper.service
[Unit]
Description=Tailscale DERP Server
After=network.target

[Service]
Type=simple
AmbientCapabilities=CAP_NET_BIND_SERVICE
Restart=always
RestartSec=3
RuntimeMaxSec=8h
User=root
Group=root
ExecStart=/usr/local/bin/derper -c /etc/derper/derper.key \
-hostname 47.103.35.100 -a :18080 -certmode manual -certdir /etc/derper -stun -stun-port 3478 -verify-clients

[Install]
WantedBy=multi-user.target
```
