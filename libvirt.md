## 创建虚拟盘

```shell
# 创建一个 20G 的系统盘 (qcow2 格式)
qemu-img create -f qcow2 /var/lib/libvirt/images/kworker_test.qcow2 20G

# 或者，如果你想更极致地复现 IO 问题，建议预分配空间的 raw 格式：
# qemu-img create -f raw /var/lib/libvirt/images/kworker_test.img 20G
```

## 创建虚拟机

```xml
<domain type='kvm'>
  <name>kworker</name>
  <memory unit='MiB'>4096</memory>
  <currentMemory unit='MiB'>4096</currentMemory>
  <vcpu placement='static'>4</vcpu>

  <os>
    <type arch='x86_64' machine='pc'>hvm</type>
    <boot dev='cdrom'/>
    <boot dev='hd'/>
  </os>

  <features>
    <acpi/>
    <apic/>
  </features>

  <cpu mode='host-passthrough'/>

  <clock offset='utc'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>

  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>restart</on_crash>

  <devices>
    <emulator>/usr/libexec/qemu-kvm</emulator>
    <controller type='scsi' index='0' model='virtio-scsi'/>

    <disk type='file' device='cdrom'>
      <driver name='qemu' type='raw'/>
      <source file='/var/lib/libvirt/images/CentOS-7-x86_64-Minimal-2009.iso'/>
      <target dev='hda' bus='ide'/>
      <readonly/>
      <boot order='1'/>
    </disk>

    <disk type='file' device='disk'>
      <alias name='disk0'/>
      <driver name='qemu' type='qcow2' cache='none' io='native'/>
      <source file='/home/fanmi/libvirt/disks/kworker-sys.qcow2'/>
      <target dev='vda' bus='virtio'/>
      <boot order='2'/>
    </disk>
    
    <disk type='file' device='disk'>
      <alias name='disk1'/>
      <driver name='qemu' type='raw' cache='none' io='native'/>
      <source file='/home/fanmi/libvirt/disks/kworker-data.raw'/>
      <target dev='vdb' bus='virtio'/>
      <boot order='3'/>
    </disk>

    <interface type='network'>
      <source network='default'/>
      <model type='virtio'/>
    </interface>

    <graphics type='vnc' port='-1' autoport='yes' listen='0.0.0.0'>
      <listen type='address' address='0.0.0.0'/>
    </graphics>

    <video>
      <model type='cirrus' vram='16384' heads='1'/>
    </video>
  </devices>
</domain>
```

