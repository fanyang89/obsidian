---
title: DAY 2
---

#pin

```bash
mkdir -p ~/fanyang/"$(date +%Y-%m-%d)"; cd ~/fanyang/"$(date +%Y-%m-%d)"

grep -i 'centos' /etc/os-release && rpm -Uvh --force http://repo-idc.gitgo.cc/mirror/centos/7/centos-sclo-rh/x86_64/Packages/d/devtoolset-11-runtime-11.1-2.el7.x86_64.rpm http://repo-idc.gitgo.cc/mirror/centos/7/centos-sclo-rh/x86_64/Packages/d/devtoolset-11-gdb-10.2-6.el7.x86_64.rpm http://repo-idc.gitgo.cc/mirror/centos/7/os/x86_64/Packages/mpfr-3.1.1-4.el7.x86_64.rpm http://repo-idc.gitgo.cc/mirror/centos/7/os/x86_64/Packages/boost-regex-1.53.0-28.el7.x86_64.rpm http://repo-idc.gitgo.cc/mirror/centos/7/os/x86_64/Packages/source-highlight-3.1.6-6.el7.x86_64.rpm http://repo-idc.gitgo.cc/mirror/centos/7/os/x86_64/Packages/ctags-5.8-13.el7.x86_64.rpm

# rpm -Uvh http://192.168.91.19/fanyang-repo/el7/x86_64/devtoolset-11-runtime-11.1-2.el7.x86_64.rpm http://192.168.91.19/fanyang-repo/el7/x86_64/devtoolset-11-gdb-10.2-6.el7.x86_64.rpm http://192.168.91.19/fanyang-repo/el7/x86_64/mpfr-3.1.1-4.el7.x86_64.rpm http://192.168.91.19/fanyang-repo/el7/x86_64/boost-regex-1.53.0-28.el7.x86_64.rpm http://192.168.91.19/fanyang-repo/el7/x86_64/source-highlight-3.1.6-6.el7.x86_64.rpm http://192.168.91.19/fanyang-repo/el7/x86_64/ctags-5.8-13.el7.x86_64.rpm

. /opt/rh/devtoolset-11/enable

ls -lah /var/crash
find /var/crash \( -name "*meta*" -o -name "*quorum*" -o -name "*urgent*" -o -name "*access-manager*" -o -name "*watchdog*" -o -name "*meta-sm-server*" -o -name "*task-main*" -o -name "*db-cluster-io*" -o -name "zbs-insight*" \) | grep zst | xargs -I{} mv {} .
touch $(rpm -q zbs) $(rpm -q glibc)
cp /usr/sbin/zbs-metad .
find . -name "*core*zst" | awk -F. '{print $4}' | xargs -I{} find /var/log/zbs -name "*{}*" | xargs -I{} cp {} .

zstd -d --rm ./*zst
```

```bash
# show keyword only
find . -name "*log*" | xargs -I{} grep -A1 'Log line format:' {} | grep 'Built by' | xargs -I{} bash -c 'echo "{}" | tr -d ";" | awk "{print \$5}"' | sort -n | uniq | tr -d 'v' | xargs -I{} bash -c 'echo $(rpm -q zbs) | sed "s/\([0-9.]*-rc[0-9]*\)\.\([0-9]*\)/{}/g"' | sed -e 's/\.git\.g[0-9a-f]*\./ /g' | awk '{printf "\"%s\" \"%s\"\n",$1,$2}'

# show debuginfo download url
find . -name "*log*" | xargs -I{} grep -A1 'Log line format:' {} | grep 'Built by' | xargs -I{} bash -c 'echo "{}" | tr -d ";" | awk "{print \$5}"' | sort -n | uniq | tr -d 'v' | xargs -I{} bash -c 'echo $(rpm -q zbs) | sed "s/\([0-9.]*-rc[0-9]*\)\.\([0-9]*\)/{}/g"' | sed -e 's/\.git\.g[0-9a-f]*\./ /g' | awk '{printf "\"%s\" \"%s\"\n",$1,$2}' | python -c 'import urllib2,urllib,sys; a=[x.strip() for x in sys.stdin]; b=[urllib2.urlopen(urllib2.Request("http://192.168.91.19:7700/indexes/files-192_168_17_20/search?"+urllib.urlencode({"q":x}), headers={"Content-Type":"application/json"}), timeout=3).read() for x in a]; import json; print("\n".join(["curl -LO "+json.loads(x)["hits"][0]["url"] for x in b]))'
```

```bash
ls | grep ".core." && ls | grep ".core." | awk -F. '{print $NF}' | xargs -I{} date -d @{}
```

# DAY 2

```bash
. /opt/rh/devtoolset-11/enable
```

# 打包带回家

```bash
export CORE_ARCHIVE=$(basename $PWD)
export CORE_ARCHIVE_FILE="core-$(hostname)-$CORE_ARCHIVE.tar.zst"
pushd ..
tar -Izstd -cvf $CORE_ARCHIVE_FILE $CORE_ARCHIVE
curl -T $CORE_ARCHIVE_FILE https://ds.fuis.me/f/core/ | cat
popd
```

```bash
#!/usr/bin/env bash

META_BASE=$1
META_PORT=$2

./src/zbs-metad --meta_port $META_PORT --alsologtostderr --foreground --log_dir "$META_BASE/logs" --base_dir "$META_BASE" --leveldb_path "$META_BASE/metad/db"
```

```bash
~/start-meta.sh $HOME/app/meta-1 10100
~/start-meta.sh $HOME/app/meta-2 20100

python3 zstart.py --base-dir $HOME/app/meta-1 --port 10100 src/zbs-metad
```

# 收集 LTTNG SESSION

```bash
ls -lah ~/lttng-traces/
tar -Izstd -cvf lttng-traces-$(hostname).tar.zst ~/lttng-traces/
curl -T lttng-traces-$(hostname).tar.zst http://192.168.31.231/f/tmp/ | cat
```
