#Hyper-V

```bash
Set-VMProcessor -VMName localdev-2 -HwThreadCountPerCore 2
Set-VMProcessor -VMName localdev-2 -ExposeVirtualizationExtensions $true
```
