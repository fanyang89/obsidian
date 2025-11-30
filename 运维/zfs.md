```
sudo zpool create -R /mnt/data -f -o ashift=9 data_pool \
disk /dev/nvme1n1p1 disk /dev/nvme2n1p1 disk /dev/nvme3n1p1 disk /dev/nvme4n1p1 \
disk /dev/nvme5n1p1 disk /dev/nvme6n1p1 disk /dev/nvme7n1p1 disk /dev/nvme8n1p1
```
