---
title: uboot
tags: ARM
categories: ARM
abbrlink: 90b9e97a
date: 2019-06-19 00:00:00
---

# U-Boot简介

uboot 官网为 http://www.denx.de/wiki/U-Boot/

NXP 就 维 护 的 2016.03 这 个 版 本 的 uboot ， 下 载 地 址 为 ：http://git.freescale.com/git/cgit.cgi/imx/uboot-imx.git/tag/?h=imx_v2016.03_4.1.15_2.0.0_ga&id=rel_imx_4.1.15_2.1.0_ga

# U-Boot编译

首先在 Ubuntu 中安装 ncurses 库，否则编译会报错

```shell
sudo apt-get install libncurses5-dev
```

编译命令

```shell
#!/bin/bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- mx6ull_14x14_ddr512_emmc_defconfig
make V=1 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j12
```

# U-Boot烧写

```shell
chmod 777 imxdownload //给予 imxdownload 可执行权限，一次即可
./imxdownload u-boot.bin /dev/sdd //烧写到 SD 卡，不能烧写到/dev/sda 或 sda1 设备里面！
```

# U-Boot启动

```
示例代码 30.3.1 uboot 输出信息
U-Boot 2016.03-gd3f0479 (Aug 07 2020 - 20:47:37 +0800) 

CPU: Freescale i.MX6ULL rev1.1 792 MHz (running at 396 MHz) 
CPU: Industrial temperature grade (-40C to 105C) at 51C
Reset cause: POR
Board: I.MX6U ALPHA|MINI
I2C: ready
DRAM: 512 MiB
MMC: FSL_SDHC: 0, FSL_SDHC: 1
Display: ATK-LCD-7-1024x600 (1024x600)
Video: 1024x600x24
In: serial
Out: serial
Err: serial
switch to partitions #0, OK
mmc1(part 0) is current device
Net: FEC1
Error: FEC1 address not set.

Normal Boot
Hit any key to stop autoboot: 0
=>
```

第 1 行是 uboot 版本号和编译时间，可以看出，当前的 uboot 版本号是 2016.03，编译时间是 2020 年 8 月 7 日凌晨 20 点 47 分。 

第 3 和第 4 行是 CPU 信息，可以看出当前使用的 CPU 是飞思卡尔的 I.MX6ULL（I.MX 以前属于飞思卡尔，然而飞思卡尔被 NXP 收购了），频率为 792MHz，但是此时运行在 396MHz。这颗芯片是工业级的，结温为-40°C~105°C。

第 5 行是复位原因，当前的复位原因是 POR。I.MX6ULL 芯片上有个 POR_B 引脚，将这个引脚拉低即可复位 I.MX6ULL。 

第 6 行是板子名字，当前的板子名字为“I.MX6U ALPHA|MINI”。

第 7 行提示 I2C 准备就绪。

第 8 行提示当前板子的 DRAM（内存）为 512MB，如果是 NAND 版本的话内存为 256MB。

第 9 行提示当前有两个 MMC/SD 卡控制器：FSL_SDHC(0)和 FSL_SDHC(1)。I.MX6ULL支持两个 MMC/SD，正点原子的 I.MX6ULL EMMC 核心板上 FSL_SDHC(0)接的 SD(TF)卡，FSL_SDHC(1)接的 EMMC。 

第 10 和第 11 行是 LCD 型号，当前的 LCD 型号是 ATK-LCD-7-1024x600 (1024x600)，分辨率为 1024x600，格式为 RGB888(24 位)。 

第 12~14 是标准输入、标准输出和标准错误所使用的终端，这里都使用串口(serial)作为终端。

第 15 和 16 行是切换到 emmc 的第 0 个分区上，因为当前的 uboot 是 emmc 版本的，也就是从 emmc 启动的。我们只是为了方便将其烧写到了 SD 卡上，但是它的“内心”还是 EMMC的。所以 uboot 启动以后会将 emmc 作为默认存储器，当然了，你也可以将 SD 卡作为 uboot 的存储器，这个我们后面会讲解怎么做。

第 17 行是网口信息，提示我们当前使用的 FEC1 这个网口，I.MX6ULL 支持两个网口。

第 18 行提示 FEC1 网卡地址没有设置，后面我们会讲解如何在 uboot 里面设置网卡地址。

第 20 行提示正常启动，也就是说 uboot 要从 emmc 里面读取环境变量和参数信息启动 Linux内核了。

第 21 行是倒计时提示，默认倒计时 3 秒，倒计时结束之前按下回车键就会进入 Linux 命令行模式。如果在倒计时结束以后没有按下回车键，那么 Linux 内核就会启动，Linux 内核一旦启动，uboot 就会寿终正寝。

# U-Boot命令

进入 uboot 的命令行模式以后输入“help”或者“？”，然后按下回车即可查看当前 uboot 所支持的命令，如下图所示：

<img src="uboot/image-20210619224146998.png" alt="uboot命令列表" style="zoom:50%;" />

## 查看命令的详细用法

```shell
? bootz 
# 或 
help bootz
```

<img src="uboot/image-20210619225750059.png" alt="bootz 命令使用说明" style="zoom:50%;" />

## 信息查询命令

### bdinfo

<img src="uboot/image-20210619225932043.png" alt="bdinfo 命令" style="zoom:50%;" />

可以得出 DRAM 的起始地址和大小、启动参数保存起始地址、波特率、sp(堆栈指针)起始地址等信息。

### printenv

命令“printenv”用于输出环境变量信息，uboot 也支持 TAB 键自动补全功能，输入“print”然后按下 TAB 键就会自动补全命令，直接输入“print”也可以。输入“print”，然后按下回车键，环境变量如下图所示：

<img src="uboot/image-20210619230103790.png" alt="printenv 命令结果" style="zoom: 50%;" />

### version

命令 version 用于查看 uboot 的版本号

<img src="uboot/image-20210619230859876.png" alt="version 命令结果" style="zoom:50%;" />

当前 uboot 版本号为 2016.03，2020 年 8 月 7 日编译的，编译器为arm-poky-linux-gnueabi-gcc，这是 NXP 官方提供的编译器

## 环境变量

### setenv 修改环境变量

将环境变量 bootdelay 改为 5，就可以使用如下所示命令

```shell
setenv bootdelay 5
saveenv
```

<img src="uboot/image-20210619232814961.png" alt="环境变量修改" style="zoom:50%;" />

<img src="uboot/image-20210619232832014.png" alt="5秒倒计时" style="zoom:50%;" />

有时候我们修改的环境变量值可能会有空格，比如 bootcmd、bootargs 等，这个时候环境变量值就得用单引号括起来，比如下面修改环境变量 bootargs 的值：

```shell
setenv bootargs 'console=ttymxc0,115200 root=/dev/mmcblk1p2 rootwait rw'
saveenv
```

上面命令设置 bootargs 的值为“console=ttymxc0,115200 root=/dev/mmcblk1p2 rootwait rw”，其中“console=ttymxc0,115200”、“root=/dev/mmcblk1p2”、“rootwait”和“rw”相当于四组“值”，这四组“值”之间用空格隔开，所以需要使用单引号‘’将其括起来，表示这四组“值”都属于环境变量 bootargs。

### setenv 新建环境变量

命令 setenv 也可以用于新建命令，用法就是修改环境变量一样，比如我们新建一个环境变量 author，author 的值为creekwater，那么就可以使用如下命令：

```shell
setenv author creekwater
saveenv
```

新建环境变量 creekwater 完成以后重启 uboot，然后使用命令 printenv 查看当前环境变量。

<img src="uboot/image-20210620180856303.png" alt="环境变量" style="zoom:50%;" />

### setevn 删除环境变量

既然可以新建环境变量，那么就可以删除环境变量，删除环境变量也是使用命令 setenv，要删除一个环境变量只要给这个环境变量赋空值即可，比如我们删除掉上面新建的 author 这个环境变量，命令如下：

```shell
setenv creekwater
saveenv
```

上面命令中通过 setenv 给 author 赋空值，也就是什么都不写来删除环境变量 author。重启uboot 就会发现环境变量 author 没有了。

## 内存操作命令

内存操作命令就是用于直接对 DRAM 进行读写操作的，常用的内存操作命令有 md、nm、mm、mw、cp 和 cmp。我们依次来看一下这些命令都是做什么的。

### md 显示内存值

md 命令用于显示内存值，格式如下：

```shell
md[.b, .w, .l] address [# of objects]
```

命令中的[.b .w .l]对应 byte、word 和 long，也就是分别以 1 个字节、2 个字节、4 个字节来显示内存值。address 就是要查看的内存起始地址，[# of objects]表示要查看的数据长度，这个数据长度单位不是字节，而是跟你所选择的显示格式有关。

比如你设置要查看的内存长度为20(十六进制为 0x14)，如果显示格式为.b 的话那就表示 20 个字节；

如果显示格式为.w 的话就表示 20 个 word，也就是 20*2=40 个字节；

如果显示格式为.l 的话就表示 20 个 long，也就是20*4=80 个字节。

>注意：uboot 命令中的数字都是十六进制的！不是十进制的！

如果查看以 0X80000000 开始的 20 个字节的内存值，显示格式为.b 的话，应该使用如下所示命令：

```shell
md.b 80000000 14
# 而不是 md.b 80000000 20
```

因为uboot 命令里面的数字都是十六进制的，所以可以不用写“0x”前缀，十进制的 20 其十六进制为 0x14，所以命令 md 后面的个数应该是 14，如果写成 20 的话就表示查看32(十六进制为 0x20)个字节的数据。分析下面三个命令的区别：

```shell
md.b 80000000 10
md.w 80000000 10
md.l 80000000 10
```

上面这三个命令都是查看以 0X80000000 为起始地址的内存数据，第一个命令以.b 格式显示，长度为 0x10，也就是 16 个字节；

第二个命令以.w 格式显示，长度为 0x10，也就是 16*2=32个字节；

最后一个命令以.l 格式显示，长度也是 0x10，也就是 16*4=64 个字节。

这三个命令的执行结果如下图所示：

<img src="uboot/image-20210619234336918.png" alt="md 命令使用示意" style="zoom:50%;" />

### nm 修改内存值

nm 命令用于修改指定地址的内存值，命令格式如下：

```shell
nm [.b, .w, .l] address
```

nm 命令同样可以以.b、.w 和.l 来指定操作格式，比如现在以.l 格式修改 0x80000000 地址的数据为 0x12345678。输入命令：

```
nm.l 80000000
```

输入上述命令以后如下图所示：

<img src="uboot/image-20210619234758340.png" alt="nm 命令" style="zoom:50%;" />

在上图中，80000000 表示现在要修改的内存地址，0500e031 表示地址 0x80000000 现在的数据，？后面就可以输入要修改后的数据 0x12345678，输入完成以后按下回车，然后再输

入‘q’即可退出，如下图所示：

<img src="uboot/image-20210619234918951.png" alt="修改内存数据" style="zoom:50%;" />

修改完成以后在使用命令 md 来查看一下有没有修改成功，如下图所示：

<img src="uboot/image-20210619235122533.png" alt="查看修改后的值" style="zoom:50%;" />

此时地址 0X80000000 的值变为了 0x12345678。

### mm 修改内存值

mm 命令也是修改指定地址内存值的，使用 mm 修改内存值的时候地址会自增，而使用命令 nm 的话地址不会自增。比如以.l 格式修改从地址 0x80000000 开始的连续 3 个内存块(3*4=12个字节)的数据为 0X05050505，操作如下图所示：

<img src="uboot/image-20210619235235921.png" alt="mm 命令" style="zoom:50%;" />

从上图可以看出，修改了地址 0X80000000、0X80000004 和 0X8000000C 的内容为0x05050505。使用命令 md 查看修改后的值，结果如图所示：

<img src="uboot/image-20210619235312726.png" alt="查看修改后的内存数据" style="zoom:50%;" />

可以看出内存数据修改成功。

### mw 填充内存

命令 mw 用于使用一个指定的数据填充一段内存，命令格式如下：

```shell
mw [.b, .w, .l] address value [count]
```

mw 命令同样可以以.b、.w 和.l 来指定操作格式，address 表示要填充的内存起始地址，value为要填充的数据，count 是填充的长度。比如使用.l 格式将以 0X80000000 为起始地址的 0x10 个内存块(0x10 * 4=64 字节)填充为 0X0A0A0A0A，命令如下：

```
mw.l 80000000 0A0A0A0A 10
```

然后使用命令 md 来查看，如下图所示：

<img src="uboot/image-20210620180352686.png" alt="查看修改后的内存数据" style="zoom:50%;" />

### cp 数据拷贝

cp 是数据拷贝命令，用于将 DRAM 中的数据从一段内存拷贝到另一段内存中，或者把 Nor Flash 中的数据拷贝到 DRAM 中。命令格式如下：

```shell
cp [.b, .w, .l] source target count
```

cp 命令同样可以以.b、.w 和.l 来指定操作格式，source 为源地址，target 为目的地址，count 为拷贝的长度。我们使用.l 格式将 0x80000000 处的地址拷贝到 0X80000100 处，长度为 0x10 个内存块(0x10 * 4=64 个字节)，命令如下所示：

```shell
cp.l 80000000 80000100 10
```

结果如图所示：

<img src="uboot/image-20210620181625687.png" alt="cp 命令操作结果" style="zoom:50%;" />

在上图中，先使用 md.l 命令打印出地址 0x80000000 和 0x80000100 处的数据，然后使用命令cp.l将0x80000100处的数据拷贝到0x80000100处。最后使用命令md.l查看0x80000100处的数据有没有变化，检查拷贝是否成功。

### cmp 内存数据比较

cmp 是比较命令，用于比较两段内存的数据是否相等，命令格式如下：

```shell
cmp [.b, .w, .l] addr1 addr2 count
```

cmp 命令同样可以以.b、.w 和.l 来指定操作格式，addr1 为第一段内存首地址，addr2 为第二段内存首地址，count 为要比较的长度。我们使用.l 格式来比较 0x80000000 和 0X80000100 这两个地址数据是否相等，比较长度为 0x10 个内存块(16 * 4=64 个字节)，命令如下所示：

```
cmp.l 80000000 80000100 10
```

结果如图

<img src="uboot/image-20210620181935193.png" alt="cmp 命令比较结果" style="zoom:50%;" />

从上图可以看出两段内存的数据相等。我们再随便挑两段内存比较一下，比如地址 0x80002000 和 0x800003000，长度为 0X10，比较结果下图所示：

<img src="uboot/image-20210620182043861.png" alt="cmp 命令比较结果" style="zoom:50%;" />

从上图可以看出，0x80002000 处的数据和 0x80003000 处的数据就不一样。

## 网络操作命令

uboot 是支持网络的，我们在移植 uboot 的时候一般都要调通网络功能，因为在移植 linux kernel 的时候需要使用到 uboot 的网络功能做调试。uboot 支持大量的网络相关命令，比如 dhcp、ping、nfs 和 tftpboot，接下来依次学习一下这几个和网络有关的命令。

在使用 uboot 的网络功能之前先用网线将开发板的 ENET2 接口和电脑或者路由器连接起来，I.MX6U-ALPHA 开发板有两个网口：ENET1 和 ENET2，一定要连接 ENET2，不能连接错了，ENET2 接口如图所示。

<img src="uboot/image-20210620182313644.png" alt="ENET2 网络接口" style="zoom:50%;" />

建议开发板和主机 PC 都连接到同一个路由器上！最后设置表中所示的几个环境变量。

| 环境变量  | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| ipaddr    | 开发板 ip 地址，可以不设置，使用 dhcp 命令来从路由器获取 IP 地址。 |
| ethaddr   | 开发板的 MAC 地址，一定要设置。                              |
| gatewayip | 网关地址。                                                   |
| netmask   | 子网掩码。                                                   |
| serverip  | 服务器 IP 地址，也就是 Ubuntu 主机 IP 地址，用于调试代码。   |

表中环境变量设置命令如下所示：

```shell
setenv ipaddr 192.168.1.50
setenv ethaddr 00:04:9f:04:d2:35
setenv gatewayip 192.168.1.1
setenv netmask 255.255.255.0
setenv serverip 192.168.1.253
saveenv
```

注意，网络地址环境变量的设置要根据自己的实际情况，确保 Ubuntu 主机和开发板的 IP地址在同一个网段内，比如我现在的开发板和电脑都在 192.168.1.0 这个网段内，所以设置开发板的 IP 地址为 192.168.1.50，我的 Ubuntu 主机的地址为 192.168.1.253，因此 serverip 就是192.168.1.253。ethaddr 为网络 MAC 地址，是一个 48bit 的地址，如果在同一个网段内有多个开发板的话一定要保证每个开发板的 ethaddr 是不同的，否则通信会有问题！设置好网络相关的环境变量以后就可以使用网络相关命令了。

### ping 命令

开发板的网络能否使用，是否可以和服务器(Ubuntu 主机)进行通信，通过 ping 命令就可以验证，直接 ping 服务器的 IP 地址即可，比如我的服务器 IP 地址为 192.168.1.253，命令如下：

```
ping 192.168.1.253
```

结果如图所示

<img src="uboot/image-20210620182910766.png" alt="ping 命令" style="zoom:50%;" />

可以看出，192.168.1.253 这个主机存在，说明 ping 成功，uboot 的网络工作正常。

>注意！只能在 uboot 中 ping 其他的机器，其他机器不能 ping uboot，因为 uboot 没有对 ping命令做处理，如果用其他的机器 ping uboot 的话会失败！

### dhcp 命令

dhcp 用于从路由器获取 IP 地址，前提得开发连接到路由器上的，如果开发板是和电脑直连的，那么 dhcp 命令就会失效。直接输入 dhcp 命令即可通过路由器获取到 IP 地址，如图所示：

<img src="uboot/image-20210620183208736.png" alt="dhcp 命令" style="zoom:50%;" />

从上图可以看出，开发板通过 dhcp 获取到的 IP 地址为 192.168.1.137。同时在上图中可以看到“warning：no boot file name;”、“TFTP from server 192.168.1.1”这样的字样。这是因为 DHCP 不单单是获取 IP 地址，其还会通过 TFTP 来启动 linux 内核，输入“? dhcp”可查看 dhcp 命令详细的信息，如下图所示：

<img src="uboot/image-20210620183337213.png" alt="dhcp 命令使用查询" style="zoom:50%;" />

### nfs 命令

nfs(Network File System)网络文件系统，通过 nfs 可以在计算机之间通过网络来分享资源，比如我们将 linux 镜像和设备树文件放到 Ubuntu 中，然后在 uboot 中使用 nfs 命令将 Ubuntu 中 的 linux 镜像和设备树下载到开发板的 DRAM 中。这样做的目的是为了方便调试 linux 镜像和设备树，也就是网络调试，通过网络调试是 Linux 开发中最常用的调试方法。原因是嵌入式 linux开发不像单片机开发，可以直接通过 JLINK 或 STLink 等仿真器将代码直接烧写到单片机内部的 flash 中，嵌入式 Linux 通常是烧写到 EMMC、NAND Flash、SPI Flash 等外置 flash 中，但是嵌入式 Linux 开发也没有 MDK，IAR 这样的 IDE，更没有烧写算法，因此不可能通过点击一个“download”按钮就将固件烧写到外部 flash 中。虽然半导体厂商一般都会提供一个烧写固件的软件，但是这个软件使用起来比较复杂，这个烧写软件一般用于量产的。其远没有 MDK、IAR的一键下载方便，在 Linux 内核调试阶段，如果用这个烧写软件的话将会非常浪费时间，而这个时候网络调试的优势就显现出来了，可以通过网络将编译好的 linux 镜像和设备树文件下载到 DRAM 中，然后就可以直接运行。

我们一般使用 uboot 中的 nfs 命令将 Ubuntu 中的文件下载到开发板的 DRAM 中，在使用之前需要开启 Ubuntu 主机的 NFS 服务，并且要新建一个 NFS 使用的目录，以后所有要通过NFS 访问的文件都需要放到这个 NFS 目录中。Ubuntu 的 NFS 服务开启前文已经详细讲解过了，包括 NFS 文件目录的创建。我设置的/home/creekwater/linux/nfs 这个目录为我的 NFS 文件目录。uboot 中的 nfs 命令格式如下所示：

```shell
nfs [loadAddress] [[hostIPaddr:]bootfilename]
```

loadAddress 是要保存的 DRAM 地址，[[hostIPaddr:]bootfilename]是要下载的文件地址。这里我们将正点原子官方编译出来的 Linux 镜像文件 zImage 下载到开发板 DRAM 的 0x80800000这个地址处。正点原子编译出来的 zImage 文件已经放到了开发板光盘中，路径为：8、系统镜像->1、出厂系统镜像->2、kernel 镜像\linux-imx-4.1.15-2.1.0-gbfed875-v1.6 ->zImage。将文件zImage 通 过 FileZilla 发送到 Ubuntu 中 的 NFS 目 录 下 ， 比 如 我 的 就 是 放 到/home/creekwater/linux/nfs 这个目录下，完成以后的 NFS 目录如图所示：

<img src="uboot/image-20210620184356976.png" alt="NFS 目录中的 zImage 文件" style="zoom:50%;" />

准备好以后就可以使用 nfs 命令来将 zImage 下载到开发板 DRAM 的 0X80800000 地址处，命令如下：

```
nfs 80800000 192.168.1.253:/home/creekwater/linux/nfs/zImage
```

命令中的“ 80800000 ” 表 示 zImage 保 存 地 址 ，“192.168.1.253:/home/creekwater/linux/nfs/zImage”表示 zImage 在 192.168.1.253 这个主机中，路径为/home/creekwater/linux/nfs/zImage。下载过程如图所示：

<img src="uboot/image-20210620184910494.png" alt="nfs 命令下载 zImage 过程" style="zoom:50%;" />

在上图中会以“#”提示下载过程，下载完成以后会提示下载的数据大小，这里下载的 6785272 字节(出厂系统在不断的更更新中，因此以实际的 zImage 大小为准)，而 zImage 的大小就是 6785272 字节，如下图所示：

<img src="uboot/image-20210620185346075.png" alt="zImage 大小" style="zoom:50%;" />

下载完成以后查看 0x80800000 地址处的数据，使用命令 md.b 来查看前 0x100 个字节的数据，如图所示：

<img src="uboot/image-20210620185801053.png" alt="下载的数据" style="zoom:50%;" />

在使用 winhex 软件来查看 zImage，检查一下前面的数据是否和上图的一致，结果如下图所示：

<img src="uboot/image-20210620185849955.png" alt="winhex 查看 zImage" style="zoom:50%;" />

可以看出两个图中的前 100 个字节的数据一致，说明 nfs 命令下载到的 zImage 是正确的。

### tftp 命令

## EMMC和SD卡操作命令

### mmc info 命令

### mmc rescan 命令

### mmc list

### mmc dev

### mmc part

### mmc read

### mmc write

### mmc erase

## FAT 格式文件系统操作命令

### fatinfo

### fatls

### fstype

### fatload

### fatwrite

## EXT 格式文件系统操作命令

## NAND 操作指令

### nand info

### nand device

### nand erase

### nand write

### nand read

## BOOT 操作命令

### bootz

### bootm

### boot

## 其他常用命令

### reset

### go

### run

### mtest

