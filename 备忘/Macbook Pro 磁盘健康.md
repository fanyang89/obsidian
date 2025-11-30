```
fanyang@fanyangdeMacBook-Pro ~ $ date
2022年 8月25日 星期四 14时19分22秒 CST
fanyang@fanyangdeMacBook-Pro ~ $ smartctl -A /dev/disk0
smartctl 7.3 2022-02-28 r5338 [Darwin 21.6.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        28 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    20,644,881 [10.5 TB]
Data Units Written:                 14,017,011 [7.17 TB]
Host Read Commands:                 308,112,992
Host Write Commands:                258,370,331
Controller Busy Time:               0
Power Cycles:                       358
Power On Hours:                     222
Unsafe Shutdowns:                   15
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```
fanyang@fanyangdeMacBook-Pro ~/Downloads $ date
2022年 9月20日 星期二 11时33分20秒 CST
fanyang@fanyangdeMacBook-Pro ~/Downloads $ smartctl -A /dev/disk0
smartctl 7.3 2022-02-28 r5338 [Darwin 21.6.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        33 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    26,041,104 [13.3 TB]
Data Units Written:                 17,072,791 [8.74 TB]
Host Read Commands:                 412,155,714
Host Write Commands:                312,033,592
Controller Busy Time:               0
Power Cycles:                       368
Power On Hours:                     288
Unsafe Shutdowns:                   15
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```
fanyang@fanyangdeMacBook-Pro ~ » smartctl -A /dev/disk0
smartctl 7.3 2022-02-28 r5338 [Darwin 21.6.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        31 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    26,814,449 [13.7 TB]
Data Units Written:                 17,372,562 [8.89 TB]
Host Read Commands:                 421,485,807
Host Write Commands:                318,941,011
Controller Busy Time:               0
Power Cycles:                       368
Power On Hours:                     296
Unsafe Shutdowns:                   15
Media and Data Integrity Errors:    0
Error Information Log Entries:      0

fanyang@fanyangdeMacBook-Pro ~ » date
2022年 9月23日 星期五 14时54分18秒 CST
```

```
$ smartctl -A /dev/disk0                                                 [14:55:38]
smartctl 7.3 2022-02-28 r5338 [Darwin 22.1.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        33 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    32,390,684 [16.5 TB]
Data Units Written:                 20,540,818 [10.5 TB]
Host Read Commands:                 513,338,815
Host Write Commands:                370,679,694
Controller Busy Time:               0
Power Cycles:                       376
Power On Hours:                     357
Unsafe Shutdowns:                   15
Media and Data Integrity Errors:    0
Error Information Log Entries:      0


fanyang@fanyangdeMacBook-Pro: ~
$ date                                                                   [15:35:49]
2022年10月27日 星期四 15时35分57秒 CST
```

```
fanyang:~/ $ date                                                                                                                                                      [13:17:41]
2023年 1月31日 星期二 13时18分20秒 CST

fanyang:~/ $ smartctl -A /dev/disk0                                                                                                                                    [13:18:20]
smartctl 7.3 2022-02-28 r5338 [Darwin 22.2.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        34 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    48,957,178 [25.0 TB]
Data Units Written:                 30,067,544 [15.3 TB]
Host Read Commands:                 822,879,861
Host Write Commands:                551,133,453
Controller Busy Time:               0
Power Cycles:                       394
Power On Hours:                     541
Unsafe Shutdowns:                   16
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```bash
-> % date
2023年 4月 7日 星期五 14时11分59秒 CST

fanyang@fanyangdeMacBook-Pro [14:11:59] [~]
-> % smartctl -A /dev/disk0
smartctl 7.3 2022-02-28 r5338 [Darwin 22.4.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        32 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    1%
Data Units Read:                    65,760,742 [33.6 TB]
Data Units Written:                 37,612,381 [19.2 TB]
Host Read Commands:                 1,482,264,902
Host Write Commands:                759,866,645
Controller Busy Time:               0
Power Cycles:                       412
Power On Hours:                     763
Unsafe Shutdowns:                   16
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```
fanyang@fanyangdeMacBook-Pro [12:57:43] [~]
-> % date
2023年 5月24日 星期三 12时57分44秒 CST

fanyang@fanyangdeMacBook-Pro [12:57:44] [~]
-> % smartctl -A /dev/disk0
smartctl 7.3 2022-02-28 r5338 [Darwin 22.5.0 arm64] (local build)
Copyright (C) 2002-22, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        30 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    1%
Data Units Read:                    79,798,050 [40.8 TB]
Data Units Written:                 44,300,631 [22.6 TB]
Host Read Commands:                 1,849,928,946
Host Write Commands:                890,832,526
Controller Busy Time:               0
Power Cycles:                       431
Power On Hours:                     895
Unsafe Shutdowns:                   21
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```
-> % date; smartctl -A /dev/disk0
2023年11月 1日 星期三 13时05分58秒 CST
smartctl 7.4 2023-08-01 r5530 [Darwin 23.1.0 arm64] (local build)
Copyright (C) 2002-23, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        39 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    1%
Data Units Read:                    117,766,463 [60.2 TB]
Data Units Written:                 61,241,434 [31.3 TB]
Host Read Commands:                 3,032,896,713
Host Write Commands:                1,345,611,872
Controller Busy Time:               0
Power Cycles:                       472
Power On Hours:                     1,351
Unsafe Shutdowns:                   29
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```
-> % date
Tue Jun 11 13:59:15 CST 2024
-> % smartctl -A /dev/disk0
smartctl 7.4 2023-08-01 r5530 [Darwin 23.5.0 arm64] (local build)
Copyright (C) 2002-23, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        35 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    2%
Data Units Read:                    216,621,803 [110 TB]
Data Units Written:                 89,266,841 [45.7 TB]
Host Read Commands:                 7,724,427,164
Host Write Commands:                2,162,104,046
Controller Busy Time:               0
Power Cycles:                       517
Power On Hours:                     2,571
Unsafe Shutdowns:                   29
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```

```
fanyang@Mac-mini [13:13:19] [~]
-> % date; smartctl -A /dev/disk0
Mon Dec 23 13:13:25 CST 2024
smartctl 7.4 2023-08-01 r5530 [Darwin 24.2.0 arm64] (local build)
Copyright (C) 2002-23, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF SMART DATA SECTION ===
SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        40 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    68,451,898 [35.0 TB]
Data Units Written:                 38,533,836 [19.7 TB]
Host Read Commands:                 1,691,523,180
Host Write Commands:                529,534,573
Controller Busy Time:               0
Power Cycles:                       135
Power On Hours:                     424
Unsafe Shutdowns:                   5
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```
