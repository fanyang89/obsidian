```shell
fanmi@ds [12:36:36] [~]
-> % sudo smartctl -i /dev/nvme1n1
[sudo] password for fanmi:
smartctl 7.1 2019-12-30 r5022 [x86_64-linux-5.4.241-24.0017.23] (local build)
Copyright (C) 2002-19, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Number:                       Micron_7450_MTFDKCE7T6TFR
Serial Number:                      241147A31B34
Firmware Version:                   E2MU200
PCI Vendor/Subsystem ID:            0x1344
IEEE OUI Identifier:                0x00a075
Total NVM Capacity:                 7,681,501,126,656 [7.68 TB]
Unallocated NVM Capacity:           0
Controller ID:                      0
Number of Namespaces:               132
Namespace 1 Size/Capacity:          7,681,501,126,656 [7.68 TB]
Namespace 1 Formatted LBA Size:     512
Namespace 1 IEEE EUI-64:            00a075 0147a31b34
Local Time is:                      Thu Aug 21 12:36:54 2025 CST


fanmi@ds [12:36:54] [~]
-> % sudo smartctl -H /dev/nvme1n1
smartctl 7.1 2019-12-30 r5022 [x86_64-linux-5.4.241-24.0017.23] (local build)
Copyright (C) 2002-19, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED


fanmi@ds [12:37:01] [~]
-> % sudo smartctl -A /dev/nvme1n1
smartctl 7.1 2019-12-30 r5022 [x86_64-linux-5.4.241-24.0017.23] (local build)
Copyright (C) 2002-19, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        44 Celsius
Available Spare:                    100%
Available Spare Threshold:          10%
Percentage Used:                    0%
Data Units Read:                    8,655,791 [4.43 TB]
Data Units Written:                 23,094,297 [11.8 TB]
Host Read Commands:                 16,298,206
Host Write Commands:                39,280,371
Controller Busy Time:               486
Power Cycles:                       44
Power On Hours:                     3,387
Unsafe Shutdowns:                   39
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
Warning  Comp. Temperature Time:    0
Critical Comp. Temperature Time:    0
Temperature Sensor 1:               52 Celsius
Temperature Sensor 2:               47 Celsius
Temperature Sensor 3:               44 Celsius
```