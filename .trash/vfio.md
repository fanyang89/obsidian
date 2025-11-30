```bash
#  lshw -c network -businfo
Bus info          Device            Class          Description
==============================================================
pci@0000:02:00.0  enp2s0            network        RTL8125 2.5GbE Controller
pci@0000:03:00.0  enp3s0            network        RTL8125 2.5GbE Controller
pci@0000:04:00.0                    network        RTL8125 2.5GbE Controller
pci@0000:05:00.0  enp5s0            network        RTL8125 2.5GbE Controller

echo "0000:04:00.0" > /sys/bus/pci/devices/0000\:04\:00.0/driver/unbind

# lspci -s 0000:04:00.0 -n
04:00.0 0200: 10ec:8125 (rev 04)

echo "10ec 8125" > /sys/bus/pci/drivers/vfio-pci/new_id
echo "0000:04:00.0" > /sys/bus/pci/drivers/vfio-pci/bind
```


04:00.0 Ethernet controller [0200]: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller [10ec:8125] (rev 04)
        Subsystem: Realtek Semiconductor Co., Ltd. Device [10ec:0123]
        Kernel driver in use: vfio-pci
        Kernel modules: r8169

```bash

```
02:00.0 0200: 10ec:8125 (rev 04)
03:00.0 0200: 10ec:8125 (rev 04)
04:00.0 0200: 10ec:8125 (rev 04)
05:00.0 0200: 10ec:8125 (rev 04)

