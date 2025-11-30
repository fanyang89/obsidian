#docker

## 常用服务

```bash
# vlmcsd：KMS 激活 vol 版本的 office 和 Windows
docker run -d -p 1688:1688 --restart=unless-stopped --name vlmcsd mikolatero/vlmcsd
slmgr /skms 192.168.91.19
slmgr /ipk <product key>
# https://docs.microsoft.com/zh-cn/windows-server/get-started/kms-client-activation-keys
# https://learn.microsoft.com/zh-cn/office/volume-license-activation/activate-office-by-using-kms
# CB7KF-BWN84-R7R2Y-793K2-8XDDG  Windows Server 2016 Datacenter
# M7XTQ-FN8P6-TTKYV-9D4CC-J462D  Windows 10 企业版 LTSC 2021
# W269N-WFGWX-YVC9B-4J6C9-T83GX  Windows 10 专业版
# 
slmgr /ato

# 启动 zoonavigator
docker run -d --network host -e HTTP_PORT=9000 --name zoonavigator --restart unless-stopped elkozmon/zoonavigator:latest
```

```
cscript ospp.vbs /sethst:kms.03k.org
cscript ospp.vbs /act
cscript ospp.vbs /dstatus
```

## docker 安装

```bash
sudo pacman -S docker
sudo install -D /dev/null /etc/docker/daemon.json
cat > /etc/docker/daemon.json << EOF
{
    "bip": "10.80.0.1/16",
    "live-restore": false,
    "experimental": true,
    "shutdown-timeout": 1,
    "ipv6": true,
    "fixed-cidr-v6": "fd00::/80",
    "ip6tables": true
}
EOF
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

```json
{
  "bip": "172.80.0.1/16",
  "live-restore": true,
  "experimental": false,
  "shutdown-timeout": 1,
  "ipv6": false,
  "fixed-cidr-v6": "fd00::/80",
  "ip6tables": true,
  "storage-driver": "btrfs",
  "proxies": {
    "http-proxy": "http://10.2.2.10:8899",
    "https-proxy": "http://10.2.2.10:8899",
    "no-proxy": "*.smtx.io,*.smartx.com,127.0.0.0/8,192.168.0.0/16"
  }
}
```
