#Windows #运维

## 显示当前 DNS

```
Get-DnsClientServerAddress -AddressFamily IPv4
```

## 打印路由表

```
route print -4
```

## OpenVPN 不从服务器拉取路由

```
route-nopull
route 192.168.0.0 255.255.0.0
```
