# 背景

那个 coroutine enter 两次的 crash，可能不只是 writesame 会有，其他 IO 也会，这个可能得优先调查影响了。
我看了下实现，其他 IO 是用 CoSync 做同步的，一样也会触发这个问题。
只是因为 writesame 的 IO 单个比较大，有 1280k，更容易触发 io cache 的收割路径
嗯，不只是内存污染，还会破坏锁语义
本来在等锁，结果提前进去了。主要是 GetVExtentLease 和 SyncGeneration 里那两个锁

# 调查日志

```c++
static const struct spdk_bdev_fn_table zbs_fn_table = {
    .destruct = blockdev_zbs_destruct,
    .submit_request = blockdev_zbs_submit_request,
    .io_type_supported = blockdev_zbs_io_type_supported,
    .get_io_channel = blockdev_zbs_get_io_channel,
};
```

```c++
        case SPDK_BDEV_IO_TYPE_WRITE_SAME:
            return blockdev_zbs_write_same(bdev_io, (struct blockdev_zbs*)bdev_io->ctx, bdev_io->ch,
                                           bdev_io->u.write.iovs, bdev_io->u.write.iovcnt, bdev_io->u.write.len,
                                           bdev_io->u.write.offset);    // 创建 coroutine

#define BLOCKDEV_ZBS_AIO(func, ch, ...)         \
    Coroutine::Create([=]() {                   \
        ch->ongoings++;                         \
        func(__VA_ARGS__);                      \
        ch->ongoings--;                         \
    })->Enter();
```

```c++
static inline void zbs_aio_write_same(struct spdk_bdev_io *bdev_io,
                                      const zbs_uuid_t &image, void *buf,
                                      size_t buf_len, uint64_t offset,
                                      size_t len) {
```

```c++
// yielding coroutines
====== coroutine 0 ======
zbs::libmeta::Meta::GetVExtentLease(unsigned __int128 const&, unsigned int, zbs::scoped_refptr<zbs::libmeta::CachedLease>*, bool, bool, unsigned char, bool) + 308 in section .text of /usr/sbin/zbs-chunkd
zbs::libzbs::InternalIOClient::SubmitIO(zbs::libzbs::IOCtx*) + 184 in section .text of /usr/sbin/zbs-chunkd
zbs::libzbs::ZbsClient::ProcessIO(zbs::libzbs::IOCtx*) + 392 in section .text of /usr/sbin/zbs-chunkd
zbs::libzbs::ZbsClient::SplitIO(zbs::libzbs::VolumeCtx*, zbs::libzbs::UIOCtx*) + 596 in section .text of /usr/sbin/zbs-chunkd
void zbs::libzbs::ZbsClient::DoIO<(zbs::chunk::MessageHeader::OP)5>(unsigned __int128 const&, std::conditional<((zbs::chunk::MessageHeader::OP)5)==((zbs::chunk::MessageHeader::OP)4), zbs::ReadIOBufArg, zbs::WriteIOBufArg>::type, unsigned int, unsigned long, zbs::IOController*, std::function<void ()> const&) + 2216 in section .text of /usr/sbin/zbs-chunkd

====== coroutine 1 ======
zbs::CoFunc::Impl<zbs::blockdev_zbs_write_same(zbs::spdk_bdev_io*, zbs::blockdev_zbs*, zbs::spdk_io_channel*, iovec*, int, unsigned long, unsigned long)::{lambda()#1}>::s_run_and_reset(zbs::CoFunc::storage_t&) + 52 in section .text of /usr/sbin/zbs-chunkd
zbs::Coroutine::LoopMain() + 60 in section .text of /usr/sbin/zbs-chunkd

(gdb) p zbs::g_iscsi_inflight_io
$1 = 24
```

# 影响

当调用 zbs_client 接口时没有创建 coroutine 时，可以触发。会进入 io cache 的 io 特征为：写，并且与 256KiB 不对齐。

```c++
bool IOCache::ShouldCache(UIOCtx* uioctx) { return uioctx->op == OP::VOLUME_WRITE && !IsAligned(uioctx, 256 * KiB); }
```

```c++
void ZbsClientProxy::Write(const zbs_uuid_t& volume_id, char* buf, uint32_t len, uint64_t offset, IOController* ctrl,
                           const Callable& done, bool resize, flags_t flags, cid_t preferred_cid) {
    struct io_option* option = ctrl->GetIOOption();
    option->resize        = resize;
    option->flags         = flags;
    option->preferred_cid = preferred_cid;
    ctrl->SetTimeoutMs(timeout_ms_);
    auto closure = new LambdaSelfDeleteClosure([=]() {
        Coroutine::Create([=]() {
            RefcntGuard g(&ongoing_ios_);
            client_->Write(volume_id, buf, len, offset, ctrl, done, nullptr);
        })->Enter();
    });
    thctx_->Sched(closure);
}
```

接口：

- `ZbsClient::Unmap`
- `ZbsClient::Write`
- `ZbsClient::WriteFd`
- `ZbsClient::SyncWrite`

访问了 `ZbsClient::Write` 的地方有：

- Blockd, `io_handler.cc`
- ZbsClientProxy（创建了新的 coroutine）

# 排查

```c++
libzbs::ZbsClient* AccessHandler::GetZbsClient() { return zbs_client_; }
```

```
$ zbs-meta pool show zbs-iscsi-datastore-1598682628943x
-------------  --------------------------------------
ID             45687e84-4ffa-4838-b13d-5b729d68eb47
Name           zbs-iscsi-datastore-1598682628943x
Storage Pool   system
Creation Time  2020-08-29 14:30:29.46585668
Replica#       2
Thin           True
Export         False
Description
Whitelist      172.16.85.25,172.16.85.27,172.16.85.28
-------------  --------------------------------------
```

```
/usr/libexec/qemu-kvm
-name guest=f81460a8-720e-488f-ab8c-b22bbc294519,debug-threads=on
-S
-object secret,id=masterKey0,format=raw,file=/var/lib/libvirt/qemu/domain-23-f81460a8-720e-488f-a/master-key.aes
-machine virt-rhel7.6.0,accel=kvm,usb=off,dump-guest-core=off,gic-version=3
-cpu host
-drive file=/usr/share/edk2.git/aarch64/QEMU_EFI-pflash.raw,if=pflash,format=raw,unit=0,readonly=on
-drive file=/var/lib/libvirt/qemu/nvram/f81460a8-720e-488f-ab8c-b22bbc294519_VARS.fd,if=pflash,format=raw,unit=1
-m size=16777216k,slots=255,maxmem=4194304000k
-realtime mlock=off
-smp 8,maxcpus=96,sockets=96,cores=1,threads=1
-numa node,nodeid=0,cpus=0-95,mem=16384
-uuid 720c6486-23f8-48b3-81d9-b22659ac36dd
-smbios type=1,manufacturer=SmartX,product=SMTX OS,version=1.0,serial=SmartX-a86014f80e728f48-ab8cb22bbc294519
-no-user-config
-nodefaults
-chardev socket,id=charmonitor,fd=59,server,nowait
-mon chardev=charmonitor,id=monitor,mode=control
-rtc base=utc
-no-shutdown
-boot menu=on,strict=on
-device pcie-root-port,port=0x8,chassis=1,id=pci.1,bus=pcie.0,multifunction=on,addr=0x1
-device pcie-root-port,port=0x9,chassis=2,id=pci.2,bus=pcie.0,addr=0x1.0x1
-device pcie-root-port,port=0xa,chassis=3,id=pci.3,bus=pcie.0,addr=0x1.0x2
-device pcie-root-port,port=0xb,chassis=4,id=pci.4,bus=pcie.0,addr=0x1.0x3
-device pcie-root-port,port=0xc,chassis=5,id=pci.5,bus=pcie.0,addr=0x1.0x4
-device pcie-root-port,port=0xd,chassis=6,id=pci.6,bus=pcie.0,addr=0x1.0x5
-device pcie-root-port,port=0xe,chassis=7,id=pci.7,bus=pcie.0,addr=0x1.0x6
-device pcie-root-port,port=0xf,chassis=8,id=pci.8,bus=pcie.0,addr=0x1.0x7
-device pcie-root-port,port=0x10,chassis=9,id=pci.9,bus=pcie.0,multifunction=on,addr=0x2
-device pcie-root-port,port=0x11,chassis=10,id=pci.10,bus=pcie.0,addr=0x2.0x1
-device pcie-root-port,port=0x12,chassis=11,id=pci.11,bus=pcie.0,addr=0x2.0x2
-device pcie-root-port,port=0x13,chassis=12,id=pci.12,bus=pcie.0,addr=0x2.0x3
-device pcie-root-port,port=0x14,chassis=13,id=pci.13,bus=pcie.0,addr=0x2.0x4
-device pcie-root-port,port=0x15,chassis=14,id=pci.14,bus=pcie.0,addr=0x2.0x5
-device pcie-root-port,port=0x16,chassis=15,id=pci.15,bus=pcie.0,addr=0x2.0x6
-device qemu-xhci,p2=8,p3=8,id=usb,bus=pci.3,addr=0x0
-device virtio-scsi-pci,id=scsi0,bus=pci.4,addr=0x0
-device virtio-serial-pci,id=virtio-serial0,max_ports=2,bus=pci.2,addr=0x0
-drive file.driver=iscsi,file.portal=127.0.0.1:3260,file.target=iqn.2016-02.com.smartx:system:zbs-iscsi-datastore-1598682628943x,file.lun=17,file.transport=tcp,file.initiator-name=iqn.2013-11.org.smartx:f81460a8-720e-488f-ab8c-b22bbc294519.f9275d40-e90c-11ea-807f-52540054f2d7.0,format=raw,if=none,id=drive-scsi0-0-0-0,serial=7e2fcee1-c17c-3090-b386-9a64721300cb,cache=none,aio=native
-device scsi-hd,bus=scsi0.0,channel=0,scsi-id=0,lun=0,drive=drive-scsi0-0-0-0,id=scsi0-0-0-0,bootindex=1,wwn=0x24d7007f7fc7f612,vendor=SmartX,product=SMTX OS,write-cache=on
-drive file=/usr/share/smartx/images/f447a24f-b3b6-4d7c-baf0-41ba2dae03db,format=raw,if=none,id=drive-scsi0-0-0-1,readonly=on
-device scsi-cd,bus=scsi0.0,channel=0,scsi-id=0,lun=1,drive=drive-scsi0-0-0-1,id=scsi0-0-0-1,bootindex=2
-netdev tap,fds=63:64:65:66,id=hostnet0,vhost=on,vhostfds=67:68:69:70
-device virtio-net-pci,mq=on,vectors=10,rx_queue_size=1024,netdev=hostnet0,id=net0,mac=52:54:00:c9:d3:76,bus=pci.1,addr=0x0
-chardev pty,id=charserial0
-serial chardev:charserial0
-add-fd set=9,fd=72
-chardev file,id=charserial1,path=/dev/fdset/9,append=on
-serial chardev:charserial1
-chardev socket,id=charchannel0,fd=71,server,nowait
-device virtserialport,bus=virtio-serial0.0,nr=1,chardev=charchannel0,id=channel0,name=org.qemu.guest_agent.0
-device usb-kbd,id=input0,bus=usb.0,port=1
-device usb-tablet,id=input1,bus=usb.0,port=2
-vnc 127.0.0.1:6
-device virtio-gpu-pci,id=video0,max_outputs=1,bus=pci.6,addr=0x0
-device virtio-balloon-pci,id=balloon0,bus=pci.5,addr=0x0
-sandbox off
-msg timestamp=on
```

![](assets/Pasted%20image%2020230314185950.png)

![](assets/Pasted%20image%2020230314195254.png)

```
>>> 1310720 / 1024
1280.0
```

```c
static void sd_config_write_same(struct scsi_disk *sdkp)
{
	struct request_queue *q = sdkp->disk->queue;
	unsigned int logical_block_size = sdkp->device->sector_size;

	if (sdkp->device->no_write_same) {
		sdkp->max_ws_blocks = 0;
		goto out;
	}

	/* Some devices can not handle block counts above 0xffff despite
	 * supporting WRITE SAME(16). Consequently we default to 64k
	 * blocks per I/O unless the device explicitly advertises a
	 * bigger limit.
	 */
	if (sdkp->max_ws_blocks > SD_MAX_WS10_BLOCKS)
		sdkp->max_ws_blocks = min_not_zero(sdkp->max_ws_blocks,
						   (u32)SD_MAX_WS16_BLOCKS);
	else if (sdkp->ws16 || sdkp->ws10 || sdkp->device->no_report_opcodes)
		sdkp->max_ws_blocks = min_not_zero(sdkp->max_ws_blocks,
						   (u32)SD_MAX_WS10_BLOCKS);
	else {
		sdkp->device->no_write_same = 1;
		sdkp->max_ws_blocks = 0;
	}

	if (sdkp->lbprz && sdkp->lbpws)
		sdkp->zeroing_mode = SD_ZERO_WS16_UNMAP;
	else if (sdkp->lbprz && sdkp->lbpws10)
		sdkp->zeroing_mode = SD_ZERO_WS10_UNMAP;
	else if (sdkp->max_ws_blocks)
		sdkp->zeroing_mode = SD_ZERO_WS;
	else
		sdkp->zeroing_mode = SD_ZERO_WRITE;

	if (sdkp->max_ws_blocks &&
	    sdkp->physical_block_size > logical_block_size) {
		/*
		 * Reporting a maximum number of blocks that is not aligned
		 * on the device physical size would cause a large write same
		 * request to be split into physically unaligned chunks by
		 * __blkdev_issue_write_zeroes() and __blkdev_issue_write_same()
		 * even if the caller of these functions took care to align the
		 * large request. So make sure the maximum reported is aligned
		 * to the device physical block size. This is only an optional
		 * optimization for regular disks, but this is mandatory to
		 * avoid failure of large write same requests directed at
		 * sequential write required zones of host-managed ZBC disks.
		 */
		sdkp->max_ws_blocks =
			round_down(sdkp->max_ws_blocks,
				   bytes_to_logical(sdkp->device,
						    sdkp->physical_block_size));
	}

out:
	blk_queue_max_write_same_sectors(q, sdkp->max_ws_blocks *
					 (logical_block_size >> 9));
	blk_queue_max_write_zeroes_sectors(q, sdkp->max_ws_blocks *
					 (logical_block_size >> 9));
}
```

# WRITE_SAME 参数协商

现象：协商到的 WRITE_SAME 大小为 1280 K。
