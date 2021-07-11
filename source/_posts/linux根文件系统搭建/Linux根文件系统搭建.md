---
title: Linux根文件系统搭建
tags: ARM
categories: ARM
abbrlink: f9852f95
date: 2021-07-11 00:00:00
---

# 简介

## /bin 目录

存放着系统需要的可执行文件，例如```ls```，```mv```等命令，该目录下所有用户都可以使用

## /dev 目录

device 的缩写，该目录下存放的文件都是和设备有关的，都是设备文件

## /etc 目录

存放配置文件

## /lib 目录

存放着Linux所必须的库文件，是共享库

## /mnt 目录

临时挂载目录，一般是空目录	

## /proc 目录

proc文件系统的挂载点，是个虚拟文件系统，里面的文件都是临时存在的，一般用来存储系统运行信息文件

## /usr 目录

不是user的缩写，而是 unix software resource的缩写，也就是Unix操作系统软件资源目录

## /var 目录

存放一些可以改变的数据

## /sbin 目录

存放一些可执行文件，**只有管理员可以使用**，主要用户系统管理

## /sys 目录

sysfs文件系统的挂载点，类似于proc文件系统的特殊文件系统，sysfs也是基于ram的文件系统，向用户提供详细的内核数据结构信息

## /opt

可选的文件、软件存放区

# BusyBox构建根文件系统

## 简介

Https://busybox.net/

## 修改源码

在NFS服务器中

```shell
mkdir rootfs
cd rootfs
tar -vxjf busybox-1.29.0.tar.bz2
```

修改busybox的顶层Makefile，添加ARCH和CROSS_COMPILE的值

```
CROSS_COMPILE ?= /usr/local/arm/gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-
ARCH ?= arm
```

修改 busybox-1.29.0/libbb/printable_string.c，找到函数 printable_string

修改libbb/unicode.c

## 配置busybox

```shell
make defconfig
make menuconfig
```

- Location->settings->build static binary(no shared libs)
- Location->settings->vi-style line editing commands
- Location->linux module utilities->simplified modutils
- Location->linux system utilities->mdev(16kb)
- Location->support->Check $LC_ALL, $LC_CTYPE and $LANG environment variables

## 编译

```shell
make
make install CONFIG_PREFIX=/home/wzy/linux/nfs/rootfs # 输出路径
```

## 根文件系统中添加lib库

```shell
mkdir lib
cd /usr/local/arm/gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf/arm-linuxgnueabihf/libc/lib
cp *so* *.a /home/wzy/linux/nfs/rootfs/lib/ -d
cd /home/wzy/linux/nfs/rootfs/lib/
rm ld-linux-armhf.so.3
cd /usr/local/arm/gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf/arm-linuxgnueabihf/libc/lib
cp ld-linux-armhf.so.3 /home/wzy/linux/nfs/rootfs/lib/
cd /usr/local/arm/gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf/arm-linux-gnueabihf/lib
cp *so* *.a /home/wzy/linux/nfs/rootfs/lib/ -d
```

## 根文件系统中urs/lib目录中添加库文件

```shell
cd /usr/local/arm/gcc-linaro-4.9.4-2017.01-x86_64_arm-linux-gnueabihf/arm-linux-gnueabihf/libc/usr/lib
cp *so* *.a /home/wzy/linux/nfs/rootfs/usr/lib/ -d
```

## 创建其他文件夹

在根文件系统中创建其他文件夹，如 dev、proc、mnt、sys、tmp 和 root 等

## 根文件系统测试

由于使用NFS挂载根文件系统，所以需要设置**uboot**中的**bootargs**环境变量中的**root**值

进入开发板的uboot命令行模式，输入命令

```shell
setenv bootargs 'console=ttymxc0,115200 root=/dev/nfs nfsroot=192.168.1.250:/home/wzy/linux/nfs/rootfs,proto=tcp rw ip=192.168.1.251:192.168.1.250:192.168.1.1:255.255.255.0::eth0:off' # 设置 bootargs
saveenv # 保存环境变量
```

然后使用**boot**命令启动Linux内核

>root=/dev/nfs nfsroot=192.168.1.250:/home/wzy/linux/nfs/rootfs,proto=tcp rw ip=192.168.1.251:192.168.1.250:192.168.1.1:255.255.255.0::eth0:off
>
>其中：
>
>192.168.1.250：服务器IP地址
>
>/home/wzy/linux/nfs/rootfs：根文件系统的存放路径
>
>proto=tcp rw：表示使用tcp协议，挂载的根文件系统为可读可写
>
>ip=192.168.1.251:192.168.1.250:192.168.1.1:255.255.255.0::eth0:off：开发板IP，服务器IP，网关，子网掩码，客户机名字（空着），设备名（网卡名），自动配置不使用（off）

## 创建/etc/init.d/rcS文件

在rcS中输入如下内容

```bash
#!/bin/sh

PATH=/sbin:/bin:/usr/sbin:/usr/bin
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/lib:/usr/lib
runlevel=S
umask 022
export PATH LD_LIBRARY_PATH runlevel

mount -a
mkdir /dev/pts
mount -t devpts devpts /dev/pts

echo /sbin/mdev > /proc/sys/kernel/hotplug
mdev -s

# 开机自启动
cd /usr/bin
./lcd_always_on
cd ..

vsftpd &
/sbin/sshd &

if [ -f "/var/lib/alsa/asound.state" ]; then
	echo "ALSA: Restoring mixer setting......"
	/sbin/alsactl -f /var/lib/alsa/asound.state restore &
fi
```

最后修改可执行权限

```shell
chmod 777 rcS
```

## 创建/etc/fstab文件

创建/etc/fstab文件，并添加如下源码

```bash
#<file system>		<mount point> 	<type>		<options>	<dump>	<pass>
proc				/proc			proc		defaults	0		0
tmpfs				/tmp			tmpfs		defaults	0		0
sysfs				/sys			sysfs		defaults	0		0
tmpfs				/dev			tmpfs		defaults	0		0
```

- File system：要挂载的特殊设备，也可以是块设备，譬如/dev/sda
- mount point：挂载点
- type：文件系统类型，例如ext2、ext3、proc、romfs、tmpfs等
- options：挂载选项
- dump：是否允许备份
- Pass：是否进行磁盘检查

## 创建/etc/inittab文件

inittab的详细内容可以参考busybox下的examples/inittab

init程序会读取/etc/inittab这个文件，改文件由若干台指令组成，以“:”分割的四个段组成，格式如下

```
<id>:<runlevels>:<action>:<process>
```

- id：指令的标识符，不能重复
- runlevels：对于busybox无意义，为空
- action：动作，如下表所示
- Process：具体的动作，比如程序、脚本或者命令等

| 动作action | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| sysinit    | 在系统初始化的时候 process 才会执行一次                      |
| respawn    | 当 process 终止以后马上启动一个新的                          |
| askfirst   | 和 respawn 类似，在运行 process 之前在控制台上显示“Please press Enter to activate this console.”。只要用户按下“Enter”键以后才会执行 process |
| wait       | 告诉 init，要等待相应的进程执行完以后才能继续执行。          |
| once       | 仅执行一次，而且不会等待 process 执行完成                    |
| restart    | 当 init 重启的时候才会执行 process                           |
| ctrlaltdel | 当按下 ctrl+alt+del 组合键才会执行 process                   |
| shutdown   | 关机的时候执行 process                                       |

参考 busybox 的 examples/inittab 文件，创建一个/etc/inittab，输入如下内容

```shell
#etc/inittab

# 系统运行以后运行这个rcS脚本
::sysinit:/etc/init.d/rcS

# 将console作为控制台终端
console::askfirst:-/bin/sh

# 重启的话运行/sbin/init
::restart:/sbin/init

# 按下ctrl+alt+del组合键的话就运行/sbin/reboot，所以该组合键可以重启系统
::ctrlaltdel:/sbin/reboot

# 关机的时候执行/bin/umount，卸载各个文件系统
::shutdown:/bin/umount -a -r

# 关机的时候执行/sbin/swapoff，关闭交换分区
::shutdown:/sbin/swapoff -a
```

## 外网测试

在 rootfs 中新建文件/etc/resolv.conf，然后在里面输入如下内容：

```shell
nameserver 114.114.114.114
nameserver 8.8.8.8
```

设置域名服务器，保存后重启开发板，ping百度可以成功

```shell
ping www.baidu.com
```

## 测试软件

在根文件系统中创建hello.c文件

```c
include <stdio.h>

int main(void)
{
	while(1) {
		printf("hello world!\r\n");
		sleep(2);
	}
	return 0;
}
```

编译代码

```shell
arm-linux-gnueabihf-gcc hello.c -o hello
```

查看可执行文件并在开发板运行代码

```
file hello
./hello
```

