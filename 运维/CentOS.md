#CentOS

# Vault

```bash
sed -i -e "s|^mirrorlist=|#mirrorlist=|g" \
-e "s|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=https://mirror.nju.edu.cn/centos-vault/7.9.2009/|g" \
/etc/yum.repos.d/CentOS-*.repo
```

```bash
# firewalld
systemctl disable --now firewalld

# selinux
setenforce 0
sed -i -r "s/^[#]*\s*SELINUX=.*/SELINUX=disabled/" /etc/selinux/config
cat /etc/selinux/config

dnf install fio git zsh -y
```

## vnc

```bash
dnf install tigervnc-server -y
vncserver -geometry 1920x1080

vncserver -kill :1
```

## VMware Tools

```bash
yum install open-vm-tools -y
systemctl enable --now vmtoolsd
```

## docker-ce

```bash
curl -fsSL https://download.docker.com/linux/centos/docker-ce.repo > /etc/yum.repos.d/docker-ce.repo
sed -i 's+download.docker.com+mirrors.tuna.tsinghua.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo

# dnf
dnf remove -y docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
dnf install -y docker-ce

# yum
yum remove -y docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
yum install -y docker-ce
```
