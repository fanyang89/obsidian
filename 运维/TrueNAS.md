#TrueNAS

```bash
virsh -c "qemu+unix:///system?socket=/run/truenas_libvirt/libvirt-sock" list
virsh -c "qemu+unix:///system?socket=/run/truenas_libvirt/libvirt-sock" attach-disk 1_fedora --persistent /dev/disk/by-id/ata-WDC_WUH721816ALE6L4_2CKXL0XJ sdb
```

```bash
systemctl restart middlewared
```
