#ESXi

```
esxcfg-route -a _target_network_IP_ _netmask_ _default_gateway_
esxcfg-route -a 192.168.100.0/24 192.168.0.1
```

```bash
esxcfg-route -a 192.168.33.2/32 172.16.30.31
```

```bash
# 关闭防火墙
esxcli network firewall unload
```
