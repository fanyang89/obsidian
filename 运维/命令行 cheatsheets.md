#shell #pin

> 列出 minio bucket 下的所有 object 对应的链接

```bash
mc ls www/obsidian --json -r | jq -r '.url + .key'
mc ls www/obsidian --json -r | jq -r '.url + (.key | @uri)'
```

## Cli tools

- fzf：模糊搜索
- tldr：快速 man
- axel：多线程下载
- thefuck
- icdiff：多栏比较
- cheat：命令行笔记本
- multitail：同时 tail 好几个文件

## 监控

- glances
- dstat
- nmon
- btop

## selinux

#CentOS

```bash
setenforce 0
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
cat /etc/selinux/config
```

## 代理

```bash
echo "proxy=127.0.0.1:7890" >> ~/.curlrc
git config --global http.https://github.com.proxy http://127.0.0.1:7890

echo "proxy=192.168.126.1:7890" >> ~/.curlrc
git config --global http.https://github.com.proxy http://192.168.126.1:7890

echo "proxy=172.17.224.1:7890" >> ~/.curlrc
git config --global http.https://github.com.proxy http://172.17.224.1:7890

echo "proxy=192.168.80.215:7891" >> ~/.curlrc
git config --global http.https://github.com.proxy http://192.168.80.215:7891

export HTTP_PROXY=http://192.168.80.215:7891
export HTTPS_PROXY=$HTTP_PROXY
```

## nano 配置

#nano

```bash
curl https://raw.githubusercontent.com/scopatz/nanorc/master/install.sh | sh
sed -i 's/include "~\\/.nano\\/html.nanorc"/#include "~\\/.nano\\/html.nanorc"/g' ~/.nanorc
sed -i 's/include "~\\/.nano\\/html.j2.nanorc"/#include "~\\/.nano\\/html.j2.nanorc"/g' ~/.nanorc
sed -i 's/include "~\\/.nano\\/etc-hosts.nanorc"/#include "~\\/.nano\\/etc-hosts.nanorc"/g' ~/.nanorc
```

## goproxy

```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## maven settings

```bash
install -D /dev/null ~/.m2/settings.xml
```

```bash
<settings>
  <proxies>
    <proxy>
      <id>default-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>192.168.126.1</host>
      <port>7890</port>
    </proxy>
    <proxy>
      <id>default-https-proxy</id>
      <active>true</active>
      <protocol>https</protocol>
      <host>192.168.126.1</host>
      <port>7890</port>
    </proxy>
  </proxies>

  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
</settings>
```

```xml
<settings>
  <proxies>
    <proxy>
      <id>default-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>192.168.90.221</host>
      <port>8899</port>
    </proxy>
    <proxy>
      <id>default-https-proxy</id>
      <active>true</active>
      <protocol>https</protocol>
      <host>192.168.90.221</host>
      <port>8899</port>
    </proxy>
  </proxies>

  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
</settings>
```

## Drop page cache

```bash
sync; echo 3 > /proc/sys/vm/drop_caches
```

## 就地修改配置文件 A=B

```bash
sed -i -r "s/^[#]*\s*SELINUX=.*/SELINUX=disabled/" /etc/selinux/config
```

## 拷贝磁盘

```bash
dd conv=sparse,fdatasync status=progress bs=1M if=/dev/vdb of=/dev/vda
```

## ssh 允许 root 登录并且修改密码

```bash
echo "Fanmi.5589" | passwd --stdin root
# or
echo 'HC!r0cks' | passwd --stdin root

sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
systemctl restart sshd
```

## 生成英语 Home 文件夹

```bash
LC_ALL=C xdg-user-dirs-update --force
```

## mount

```bash
mount -t cifs //10.1.2.3/fanyang /mnt/fanyang -o username=fanmi,password=Fanmi.5589
```

## 设置 github insteadof

```bash
git config --global url.ssh://git@github.com/.insteadOf https://github.com/
```

## docker 发送镜像

```bash
docker save zdev:master | bzip2 | pv | ssh fanmi@192.168.126.18 docker load
```

## NetworkManager

```
sudo nmcli n on
```

## 增大 max_user_watches

```bash
sed -i -n -e '/^\s*fs.inotify.max_user_watches=/!p' -e '$afs.inotify.max_user_watches=524288' /etc/sysctl.conf
sysctl -p
```

## ssh-rsa 废弃 workaround

```bash
cat /etc/ssh/ssh_config
# ...
PubkeyAcceptedAlgorithms +ssh-rsa
```

```
sysctl -w kernel.softlockup_panic=1
```

## 修改 home 目录中文

```bash
LANG=C xdg-user-dirs-update
```

## 卷王排名

```bash
python -m pip install gitfame
```

# 移除 sb 后缀

```bash
function trim_uuid() {
    eval "$(ls | python3 -c 'import sys,os;a=[x.strip() for x in sys.stdin if "meta" not in x];s=os.path.commonprefix([w[::-1] for w in a])[::-1];print("\n".join(["mv {} {}".format(x, x.removesuffix(s)) for x in a]))')"
}
```

# 安装 devtoolset-11-gdb

```bash
rpm -Uvh http://repo-bj.gitgo.cc/mirror/centos/7/centos-sclo-rh/x86_64/Packages/d/devtoolset-11-runtime-11.1-2.el7.x86_64.rpm http://repo-bj.gitgo.cc/mirror/centos/7/centos-sclo-rh/x86_64/Packages/d/devtoolset-11-gdb-10.2-6.el7.x86_64.rpm http://repo-bj.gitgo.cc/mirror/centos/7/os/x86_64/Packages/mpfr-3.1.1-4.el7.x86_64.rpm http://repo-bj.gitgo.cc/mirror/centos/7/os/x86_64/Packages/boost-regex-1.53.0-28.el7.x86_64.rpm http://repo-bj.gitgo.cc/mirror/centos/7/os/x86_64/Packages/source-highlight-3.1.6-6.el7.x86_64.rpm http://repo-bj.gitgo.cc/mirror/centos/7/os/x86_64/Packages/ctags-5.8-13.el7.x86_64.rpm

. /opt/rh/devtoolset-11/enable
```

# 调试命令行 zbs-client-py3

```bash
/usr/local/venv/zbs-client-py/bin/python3 /usr/local/venv/zbs-client-py/lib/python3.10/site-packages/zbs/meta/cmd.py
/usr/local/venv/zbs-client-py/bin/python3 -m cProfile /usr/local/venv/zbs-client-py/lib/python3.10/site-packages/zbs/meta/cmd.py --meta_ip 10.1.18.74 session list
```

## 根据 coredump pid 复制日志

```bash
ls | xargs -I{} echo {} | awk -F. '{print $3}' | xargs -I{} find /var/log/zbs -name "*{}*" | grep -v lsm2db | xargs -I{} cp {} .
```

## 日常

- fzf：模糊搜索
- tldr：快速 man
- axel：多线程下载
- thefuck
- icdiff：多栏比较
- cheat：命令行笔记本
- multitail：同时 tail 好几个文件

## 监控

- glances
- dstat
- nmon

## zsh 配置

```bash
echo "proxy=192.168.126.1:7890" >> ~/.curlrc
git config --global http.https://github.com.proxy http://192.168.126.1:7890

sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/agkozak/zsh-z $ZSH_CUSTOM/plugins/zsh-z

sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="dpoggi"/g' ~/.zshrc
sed -i 's/# HYPHEN_INSENSITIVE="true"/HYPHEN_INSENSITIVE="true"/g' ~/.zshrc
sed -i 's/plugins=(git)/plugins=(git docker kubectl zsh-autosuggestions zsh-completions zsh-syntax-highlighting zsh-z)/g' ~/.zshrc
echo -e "export TERM=xterm\\n" >> ~/.zshrc
echo -e "export ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=5'\\n" >> ~/.zshrc
source ~/.zshrc

cat > /usr/bin/shopt << EOF
#!/usr/bin/env bash

args='';
for item in $@
  do
    args="$args $item";
  done
shopt $args;
EOF
chmod +x /usr/bin/shopt
```

## nano 配置

```bash
curl https://raw.githubusercontent.com/scopatz/nanorc/master/install.sh | sh
sed -i 's/include "~\\/.nano\\/html.nanorc"/#include "~\\/.nano\\/html.nanorc"/g' ~/.nanorc
sed -i 's/include "~\\/.nano\\/html.j2.nanorc"/#include "~\\/.nano\\/html.j2.nanorc"/g' ~/.nanorc
sed -i 's/include "~\\/.nano\\/etc-hosts.nanorc"/#include "~\\/.nano\\/etc-hosts.nanorc"/g' ~/.nanorc
```

## docker 安装

```bash
sudo pacman -S docker
sudo install -D /dev/null /etc/docker/daemon.json
cat > /etc/docker/daemon.json << EOF
{
    "insecure-registries": [
        "docker.smartx.com:5000",
        "harbor.smartx.com",
        "registry.iomesh.com"
    ],
    "registry-mirrors": [
        "https://30gblc79.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn"
    ],
    "live-restore": true,
    "storage-driver": "btrfs",
    "log-driver": "json-file",
    "log-level": "warn",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
EOF
sudo systemctl enable --now docker
sudo usermod -aG docker $USER

```

## goproxy

```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## maven settings

```bash
install -D /dev/null ~/.m2/settings.xml
```

```bash
<settings>
  <proxies>
    <proxy>
      <id>default-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>192.168.126.1</host>
      <port>7890</port>
    </proxy>
    <proxy>
      <id>default-https-proxy</id>
      <active>true</active>
      <protocol>https</protocol>
      <host>192.168.126.1</host>
      <port>7890</port>
    </proxy>
  </proxies>

  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>

    </mirror>
  </mirrors>
</settings>
```

## Drop page cache

```bash
sync; echo 3 > /proc/sys/vm/drop_caches
```
