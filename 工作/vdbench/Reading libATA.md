#Linux #libata

[libATA Developer’s Guide](https://www.kernel.org/doc/html/latest/driver-api/libata.html#libata-developer-s-guide "Permalink to this heading")

libATA 在 Kernel 中，支持 ATA host controller 和 devices

libATA provides

- an ATA driver API
- class transports for ATA and ATAPI devices
- SCSI <-> ATA translation for ATA devices according to the T10 SAT specification

# 错误处理

qc：ata_queued_cmd
Once issued, all qc’s are either completed with [`ata_qc_complete()`](https://www.kernel.org/doc/html/latest/driver-api/libata.html#c.ata_qc_complete "ata_qc_complete") or time out.

- For commands which are handled by interrupts, `ata_host_intr()` invokes [`ata_qc_complete()`](https://www.kernel.org/doc/html/latest/driver-api/libata.html#c.ata_qc_complete "ata_qc_complete")
- or PIO tasks, pio_task invokes [`ata_qc_complete()`](https://www.kernel.org/doc/html/latest/driver-api/libata.html#c.ata_qc_complete "ata_qc_complete")
- In error cases, packet_task may also complete commands.
