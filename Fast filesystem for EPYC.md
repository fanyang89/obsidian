```bash
-> % sudo lshw -c storage -short
H/W path              Device           Class          Description
=================================================================
/0/120/1.4/0          /dev/nvme0       storage        ZHITAI TiPro7000 2TB
/0/120/1.5/0          /dev/nvme1       storage        SOLIDIGM SSDPFKNU010TZ

/0/120/3.1/0          /dev/nvme2       storage        Samsung SSD 990 EVO Plus 1TB
/0/120/3.2/0          /dev/nvme3       storage        Samsung SSD 990 EVO Plus 1TB
/0/120/3.3/0          /dev/nvme4       storage        Samsung SSD 990 EVO Plus 1TB
/0/120/3.4/0          /dev/nvme5       storage        Samsung SSD 990 EVO Plus 1TB

/0/118/1.1/0          /dev/nvme6       storage        Samsung SSD 990 EVO Plus 1TB
/0/118/1.2/0          /dev/nvme7       storage        Samsung SSD 990 EVO Plus 1TB
/0/118/1.3/0          /dev/nvme8       storage        Samsung SSD 990 EVO Plus 1TB
/0/118/1.4/0          /dev/nvme9       storage        Samsung SSD 990 EVO Plus 1TB

/0/110/3.1/0          /dev/nvme10      storage        INTEL SSDPED1D280GA

/0/100/1.1/0          /dev/nvme11      storage        Samsung SSD 990 EVO Plus 1TB
/0/100/1.2/0          /dev/nvme12      storage        Samsung SSD 990 EVO Plus 1TB
/0/100/1.3/0          /dev/nvme13      storage        Samsung SSD 990 EVO Plus 1TB
/0/100/1.4/0          /dev/nvme14      storage        Samsung SSD 990 EVO Plus 1TB
```

```bash
sudo parted /dev/nvme10n1 -s mklabel gpt

sudo parted /dev/nvme2n1 -s mklabel gpt
sudo parted /dev/nvme3n1 -s mklabel gpt
sudo parted /dev/nvme4n1 -s mklabel gpt
sudo parted /dev/nvme5n1 -s mklabel gpt

sudo parted /dev/nvme6n1 -s mklabel gpt
sudo parted /dev/nvme7n1 -s mklabel gpt
sudo parted /dev/nvme8n1 -s mklabel gpt
sudo parted /dev/nvme9n1 -s mklabel gpt

sudo parted /dev/nvme11n1 -s mklabel gpt
sudo parted /dev/nvme12n1 -s mklabel gpt
sudo parted /dev/nvme13n1 -s mklabel gpt
sudo parted /dev/nvme14n1 -s mklabel gpt
```

```bash
sudo parted /dev/nvme2n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme3n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme4n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme5n1 -s mkpart ext4 0% 100%

sudo parted /dev/nvme6n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme7n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme8n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme9n1 -s mkpart ext4 0% 100%

sudo parted /dev/nvme11n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme12n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme13n1 -s mkpart ext4 0% 100%
sudo parted /dev/nvme14n1 -s mkpart ext4 0% 100%
```

```bash
sudo zpool create tank -o ashift=12 -o autotrim=on raidz2       \
/dev/nvme2n1p1 /dev/nvme3n1p1 /dev/nvme4n1p1    /dev/nvme5n1p1  \
/dev/nvme6n1p1 /dev/nvme7n1p1 /dev/nvme8n1p1    /dev/nvme9n1p1  \
/dev/nvme11n1p1 /dev/nvme12n1p1 /dev/nvme13n1p1 /dev/nvme14n1p1

sudo zpool add tank log /dev/nvme10n1
```

```
echo "write through" | sudo tee /sys/block/nvme2n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme3n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme4n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme5n1/queue/write_cache

echo "write through" | sudo tee /sys/block/nvme6n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme7n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme8n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme9n1/queue/write_cache

echo "write through" | sudo tee /sys/block/nvme11n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme12n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme13n1/queue/write_cache
echo "write through" | sudo tee /sys/block/nvme14n1/queue/write_cache
```
