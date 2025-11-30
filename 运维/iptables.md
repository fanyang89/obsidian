```
sudo iptables -t nat -A PREROUTING -d 123.123.123.123 -j DNAT --to-destination 192.168.90.221
sudo iptables -t nat -A POSTROUTING -j MASQUERADE
```
