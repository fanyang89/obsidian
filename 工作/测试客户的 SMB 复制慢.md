---
title: SMB
---

# SMB

> https://cloud.tencent.com/developer/ask/sof/116400421
> https://kb.synology.cn/zh-cn/DSM/tutorial/What_can_I_do_when_the_file_transfer_via_Windows_SMB_CIFS_is_slow
> https://www.zhihu.com/question/68527274
> https://learn.microsoft.com/zh-cn/windows-server/administration/performance-tuning/role/file-server/smb-file-server
> https://learn.microsoft.com/zh-cn/archive/blogs/josebda/windows-server-2012-file-server-tip-new-per-share-smb-client-performance-counters-provide-great-insight
> https://learn.microsoft.com/zh-cn/troubleshoot/windows-server/networking/slow-smb-file-transfer
> https://learn.microsoft.com/zh-cn/windows-server/administration/performance-tuning/subsystem/cache-memory-management/troubleshoot#remote-file-dirty-page-threshold-is-consistently-exceeded

https://www.zhihu.com/question/534651218/answer/1903106925425586341

> 最后，我发现微软官方有个指南《在 Windows 中检测、启用和禁用 SMBv1、SMBv2 和 SMBv3》提到了，对SMB V1协议如何快速通过命令检测、禁用、启用（需要重启计算机）
>
> - 检测：
>   Get-SmbServerConfiguration | Select EnableSMB1Protocol

    - 禁用：

Set-SmbServerConfiguration -EnableSMB1Protocol $false - 启用：
Set-SmbServerConfiguration -EnableSMB1Protocol $true

linux smb 复制文件到 Windows 速度慢的问题 - 罗辑的文章 - 知乎

https://zhuanlan.zhihu.com/p/538100336

![|400](Pasted%20image%2020250515175123.png)

无 Windows defender

![|200](Pasted%20image%2020250515181350.png)

有 Windows defender（联网）

![|300](Pasted%20image%2020250515183912.png)

![|600](Pasted%20image%2020250515181735.png)

有 Windows defender（不联网）

![|200](Pasted%20image%2020250515185339.png)

robocopy ，两端无 wd

![](Pasted%20image%2020250516152940.png)

fastcopy ，两端无 wd

![](Pasted%20image%2020250516153109.png)

win explorer, 两端无 wd，1:49

```bash
Set-SmbClientConfiguration -EnableBandwidthThrottling 0 -EnableLargeMtu 1
```

![](Pasted%20image%2020250516153842.png)

无区别

https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/overview-server-message-block-signing

关闭加密

```bash
Set-SmbShare –Name <sharename> -EncryptData $true
Set-SmbServerConfiguration –EncryptData $true
```

![](Pasted%20image%2020250516154823.png)

无效

> SMB 签名的策略位于计算机配置>Windows 设置>安全设置>本地策略>安全选项中。
>
> Microsoft 网络客户端：对通信进行数字签名（始终）
> 注册表项：HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\LanManWorkstation\Parameters
> 注册表值：RequireSecuritySignature
> 数据类型：REG_DWORD
> 数据：0（禁用），1（启用）
>
> Microsoft 网络服务器：对通信进行数字签名（始终）
> 注册表项：HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters
> 注册表值：RequireSecuritySignature
> 数据类型：REG_DWORD数据：0（禁用），1（启用）

签名均未开启

测试 win7
