```
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://120.243.132.220:40365, err: wait resp error: conn closed during wait handshake response
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://112.83.175.21:8351
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://112.83.175.21:8351, err: wait resp error: conn closed during wait handshake response
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: udp://0.0.0.0:11010, remote: udp://120.194.222.30:42973
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://119.162.11.221:7282
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://119.162.11.221:7282, err: wait resp error: conn closed during wait handshake response
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://219.155.186.248:29366
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://219.155.186.248:29366, err: wait resp error: conn closed during wait handshake response
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://61.52.56.47:62599
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://61.52.56.47:62599, err: wait resp error: conn closed during wait handshake response
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://183.211.80.145:6312
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://183.211.80.145:6312, err: wait resp error: conn closed during wait handshake response
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://120.194.222.30:47630
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: udp://0.0.0.0:11010, remote: udp://123.168.156.248:15359, err: wait resp error: wait handshake timeout: Elapsed(())
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] new connection accepted. local: tcp://0.0.0.0:11010, remote: tcp://60.233.16.53:30867
Aug 18 22:50:14 fanmi-ecs easytier-core[1128068]: 2025-08-18 22:50:14: [bbb33b58-05dc-45d1-bf2f-7db6a6dc2979] connection error. local: tcp://0.0.0.0:11010, remote: tcp://60.233.16.53:30867, err: wait resp error: conn closed during wait handshake response
```

```bash
# Fedora 42
sudo dnf install fail2ban

# get logs
sudo journalctl -r -u easytier | head -n +10000 > et.log

# find ip to ban
cat et.log | grep 'wait resp error' | awk '{print $14}' | awk -F: '{print $2}' | sort -n | uniq -c | sort -n | awk '{if($1>50){print $2}}' | tr -d '//' | sort -n > et-blocklist.txt

# ban the IPs

```
