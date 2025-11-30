```bash
#!/bin/bash

target_directory="./files"

if [ ! -d "$target_directory" ]; then
  mkdir -p "$target_directory"
fi

# 进入目标目录
cd "$target_directory"

# 创建1000个1KB大小的文件并填充随机数据
for i in $(seq 1000); do dd if=/dev/urandom of="test-file$i" bs=1k count=1 2>/dev/null; done

echo "Created 1000 files with 1KB of random data each in directory $target_directory."
```


```
# in wsl
fanmi@fanmi-pc-office [12:37:05] [~]
-> % time bash -c 'for i in $(seq 1000); do dd if=/dev/urandom of="test-file$i" bs=1k count=1 2>/dev/null; done'
bash -c   0.28s user 0.07s system 107% cpu 0.329 total

# drvfs
-> % time bash -c 'for i in $(seq 1000); do dd if=/dev/urandom of="test-file$i" bs=1k count=1 2>/dev/null; done'
bash -c   0.32s user 0.08s system 36% cpu 1.114 total

# NTFS
fanmi@fanmi-pc-office [12:38:41] [/mnt/c/tmp]
-> % time bash -c 'for i in $(seq 1000); do dd if=/dev/urandom of="test-file$i" bs=1k count=1 2>/dev/null; done'
bash -c   0.35s user 0.09s system 30% cpu 1.415 total

# NFS

```