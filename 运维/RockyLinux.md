#RockyLinux

```python
#!/usr/bin/env python3

import os
import glob
import importlib
import sys

try:
    import in_place
except ImportError:
    print("Trying to Install required module: in-place")
    os.system('python3 -m pip install in-place')
    os.system('python3 ' + sys.argv[0])
    sys.exit(0)

BASE_DIR = "/etc/yum.repos.d/"
PARAM_COUNTRY = "&country=CN"

repo_files = glob.glob(f"{BASE_DIR}/*repo")

for repo in repo_files:
    with in_place.InPlace(repo, backup_ext='.bak') as fp:
        for line in fp:
            if line.startswith("mirrorlist=") and "mirrors.rockylinux.org/mirrorlist?arch=" in line and not line.strip().endswith(PARAM_COUNTRY):
                fp.write(line.strip() + PARAM_COUNTRY + "\n")
            else:
                fp.write(line)
```

```bash
dnf groupinstall 'Server with GUI'
systemctl set-default graphical.target
```

```bash
systemctl disable --now firewalld

# selinux
setenforce 0
sed -i -r "s/^[#]*\s*SELINUX=.*/SELINUX=disabled/" /etc/selinux/config
cat /etc/selinux/config
```

```
/proc/sys/kernel/core_pattern
```
