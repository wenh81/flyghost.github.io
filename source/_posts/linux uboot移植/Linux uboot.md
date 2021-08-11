---
title: Linux uboot移植
tags: ARM
categories: ARM
abbrlink: e3a5220d
date: 2019-07-11 00:00:00
---

编译NXP官方uboot，烧入开发板

```shell
U-Boot 2016.03 (Aug 08 2021 - 05:35:02 -0700)

CPU:   Freescale i.MX6ULL rev1.1 69 MHz (running at 396 MHz)
CPU:   Industrial temperature grade (-40C to 105C) at 37C
Reset cause: POR
Board: MX6ULL 14x14 EVK
I2C:   ready
DRAM:  512 MiB
MMC:   FSL_SDHC: 0, FSL_SDHC: 1
unsupported panel TFT7016
In:    serial
Out:   serial
Err:   serial
switch to partitions #0, OK
mmc0 is current device
Net:   Board Net Initialization Failed
No ethernet found.
Normal Boot
Hit any key to stop autoboot:  0 
=> 
```

检查SD卡和EMMC驱动检查

```shell
=> mmc list
FSL_SDHC: 0 (SD)
FSL_SDHC: 1
=> 
```

```shell
=> mmc dev 0
switch to partitions #0, OK
mmc0 is current device

=> mmc info
Device: FSL_SDHC
Manufacturer ID: 3
OEM: 5344
Name: SC16G 
Tran Speed: 50000000
Rd Block Len: 512
SD version 3.0
High Capacity: Yes
Capacity: 14.8 GiB
Bus Width: 4-bit
Erase Group Size: 512 Bytes
```



```shell
=> mmc dev 1
switch to partitions #0, OK
mmc1(part 0) is current device
=> 

=> mmc info
Device: FSL_SDHC
Manufacturer ID: 15
OEM: 100
Name: 8GTF4 
Tran Speed: 52000000
Rd Block Len: 512
MMC version 4.0
High Capacity: Yes
Capacity: 7.3 GiB
Bus Width: 8-bit
Erase Group Size: 512 KiB
=> 

```

修改了LCD和网卡驱动后的UBOOT启动

```shell
U-Boot 2016.03 (Aug 09 2021 - 00:16:32 +0800)

CPU:   Freescale i.MX6ULL rev1.1 69 MHz (running at 396 MHz)
CPU:   Industrial temperature grade (-40C to 105C) at 35C
Reset cause: POR
Board: MX6ULL 14x14 EVK
I2C:   ready
DRAM:  512 MiB
MMC:   FSL_SDHC: 0, FSL_SDHC: 1
Display: TFT7016 (1024x600)
Video: 1024x600x24
In:    serial
Out:   serial
Err:   serial
switch to partitions #0, OK
mmc0 is current device
Net:   FEC1
Normal Boot
Hit any key to stop autoboot:  0 
=> 
```

