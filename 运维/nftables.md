```
sudo nft create table ip filter
sudo nft add chain ip filter input { type filter hook input priority 0 \; }
sudo nft add rule ip filter input tcp dport 3888 counter drop

sudo nft list chain ip filter input
sudo nft delete rule ip filter input handle n
```
