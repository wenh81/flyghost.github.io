---
title: filesystem
date: 2021-01-05 00:00:00
swiper: false
swiperImg: '/medias/5.jpg'
top: false
tags: 乐鑫
---

# littleFS

## 功能

- **Power-loss resilience 断电恢复能力** - littlefs设计用于处理随机电源故障。所有文件操作都有强的“写时拷贝”保证，如果断电，文件系统将恢复到上一次已知的良好状态。
- **Dynamic wear leveling 动态磨损均衡** - littlefs设计时考虑了闪存，并提供动态块上的磨损均衡。此外，littlefs可以检测坏块并在它们周围工作。
- **Bounded RAM/ROM 有界RAM/ROM** - littlefs设计目的使用少量内存。RAM的使用是有严格限制的，这意味着RAM消耗不会随着文件系统的增长而改变。文件系统不包含无限递归，动态内存仅限于静态提供的可配置缓冲区。

# SPIFFS

Spiffs是一个用于嵌入式目标上的SPI NOR flash设备的文件系统。

## 介绍

- 小(嵌入式)目标，没有堆的少量RAM
- 只有大范围的数据(块)才能被删除
- 擦除将把所有块中的位重置为1
- 写操作把1变成0
- 0只能被擦除成1
- 静态磨损均衡

## 功能

能做：

- 使用**静态大小的ram缓冲区**，与文件的数量无关

- **类可移植操作系统接口**: open, close, read, write, seek, stat,等

- 多个spiffs配置可以在相同的目标上运行—甚至可以在相同的SPI闪存设备上运行

- **静态磨损均衡**

- **内置文件系统一致性检查**

不能做什么:

- **spiffs不支持目录**。它产生一个平面结构。使用路径tmp/myfile.txt创建文件将创建一个名为tmp/myfile.txt的文件，而不是在tmp目录下创建一个名为myfile.txt的文件。

- **非实时堆栈**。一个写操作的持续时间可能比另一个长得多。

- **可伸缩性较差**。Spiffs适用于小型内存设备——SPI flash的正常大小。超过~128Mbyte就不推荐了。

- **不能检测或处理坏块**。

- 一个配置，一个二进制。没有通用的spiffs二进制可以处理所有类型的配置。

# littleFS-SPIFFS-FATFS

## 静态系统资源

###  littleFS-SPIFFS

对比静态系统资源，主要比较编译后的bin文件的text/data/bss段的大小。

```txt
text段：存放代码执行语句 
data段：存放已初始化的全局变量和局部静态变量 
bss段：未初始化的全局变量和局部静态变量
```
设计了下面的Shell命令统计静态信息
```bash
find -name "*.o" -exec size {} \; | awk '
	BEGIN{
		datasum = 0;
		textsum = 0;
		bsssum = 0;
	};
	/data/{
		getline;
		textsum += $1;
		datasum += $2;
		bsssum += $3;
	};
	END{
		printf "text %d\ndata %d\nbss %d\n", textsum, datasum, bsssum
	}
'
```

统计结果如下：

| FS     |  text | data | bss  |
| :------: | :----: | :--: | :----: |
| littleFS | 24000 |  0   |   0   |
| SPIFFS |   36940 |  0  |    0  |

> 可以发现，SPIFFS的代码量比LittleFS多53%，也就意味着SPIFFS需要更多的内存存放代码。

### littleFS-FATFS

![filesystem.md](fatfs_littlefs_romram.png)

> littleFS最小的资源占用



## 空间利用率(littleFS-SPIFFS)

文件系统需要额外空间存放一些元数据，因此用户可用的空间实际大小并不直接等于分区大小。在 4896KB 的分区上分别挂载SPIFFS和Littlefs两个文件系统，他们的可用空间是多少呢？

空的文件系统体现不出来，我们试着往不同文件系统中存放一些资源文件，再观察使用情况。

这些资源文件在ubuntu的ext4 (块大小为4K）中统计有2794KB的大小。以此为标准，我们看看SPIFFS和LittleFS的空间使用情况

|    FS    | used(KB) | total(KB) | note             |
| :------: | :------: | :-------: | :--------------- |
|  SPIFFS  |   2722   |   4607    |                  |
| LittleFS |   3680   |   4896    | block size = 32K |
| LittleFS |   2800   |   4896    | block size = 4K  |

由于LittleFS的块大小决定了文件存储的最小单位，因此分别统计块大小为4K和32K的空间使用情况。当块越大，则越有可能会出现空间浪费的情况。例如32K的块大小，即使文件内容只有1KB，LittleFS也会为其分配32K的空间，造成了31K的空间浪费。

> 从空间利用率来看，SPIFFS略优于LittleFS。



## 掉电安全与修复（littleFS-SPIFFS-FATFS）

文件系统的掉电安全指在任意一时刻掉电，文件系统依然能保证其一致性，文件系统允许丢失掉电时没写完整的数据，但不能奔溃。

通过读写掉电的方式验证掉电安全，即在读写压测时随机时间掉电，再次上电后需要保证文件系统正常运行且已写入的文件数据不丢失。

SPIFFS掉电后需要调用`SPIFFS_check()`进行修复，否则无法保证文件系统一致性。这就类似于ext4与e2fsck的关系。

在实测中，4896KB的空间，SPIFFS的修复需要510s，在项目中已经放弃对其的掉电压测。

与SPIFFS相反，LittleFS在设计时就保证了掉电安全的问题，因此不需要每次掉电后执行修复工作。

而2.1.3版本的LittleFS在10W次的掉电测试中，在数万次掉电后小概率出现文件系统奔溃导致不能正确挂载的情况，是LittleFS本身逻辑的问题。在2.2版本解决。

littlefs具有强大的copy-on-write保证，并且磁盘上的存储总是保持有效状态。

|   littleFS    |  SPIFFS  | FATFS |
| :-----------: | :-----------: | :-----------: |
| copy-on-write | 修复较慢 |  无   |



## 读写性能

当空间使用率不同，测试的性能有可能不一样，在SPIFFS中尤其明显。

| IO操作 | 空闲空间 |    SPIFFS    | LittleFS(32K Block) | LittleFS(4K Block) |
| :----: | :------: | :----------: | :-----------------: | :----------------: |
|   读   |   20%    | 6095.24 KB/s |    8629.21 KB/s     |    7529.41 KB/s    |
|   读   |   100%   | 6564.10 KB/s |    8727.27 KB/s     |    7314.29 KB/s    |
|   写   |   20%    |  21.64 KB/s  |     142.54 KB/s     |    121.92 KB/s     |
|   写   |   100%   | 128.49 KB/s  |     155.37 KB/s     |    127.87 KB/s     |

> 在读写性能表现上，SPIFFS最差，且剩余空间越少，SPIFFS写性能越差。LittleFS的性能相对比较高和稳定，块大小会直接影响到写性能。



## 动态内存

动态内存指通过`malloc`申请分配的堆内存大小。不管是SPIFFS还是LittleFS都是为小系统设计的，其内存使用情况都经过精心设计，内存占用非常小。

LittleFS会为每个打开的文件单独申请一个`cache_size`的内存，在测试时，cache_size 为256B。为了对比的公平性，我们假设SPIFFS和LittleFS打开相同数量的文件情况下统计内存。

| LittleFS | SPIFFS |
| :------: | :----: |
|  3856 B  | 3790 B |

两者使用动态内存的情况相近，在最大打开数量的情况下，SPIFFS使用动态内存略少。

由于SPIFFS的内存使用并不会随着打开文件增加而增加，也就意味着，在打开少量文件的情况下，LittleFS会比SPIFFS更少的动态内存使用量。



## CPU占用

在读写压测时，统计进程使用的CPU资源百分比

| LittleFS (32K) | LittleFS (4K) | SPIFFS |
| :------------: | :-----------: | :----: |
|     22~27      |     27~50     | 44~80  |

可以发现，在相同情况下，LittleFS的CPU占用率会比SPIFFS少。



## 磨损均衡（littleFS-SPIFFS-FATFS）

![filesystem.md](dynamic_wear_leveling.gif)

| LittleFS | SPIFFS | FATFS |
| :------: | :----: | :----: |
|  动态  | 静态 | 无 |



# littleFS-SPIFFS-FATFS在ESP上对比

## 格式化一个512K的分区

```
FAT:         963,766 us
SPIFFS:   10,824,054 us
LittleFS:  2,067,845 us
```

## 写入588K的文件

```
FAT:         13,601,171 us
SPIFFS*:    118,883,197 us
LittleFS**:   6,582,045 us
LittleFS***:  5,734,811 us
*Only wrote 374,784 bytes instead of the benchmark 440,000, so this value is extrapolated
**CONFIG_LITTLEFS_CACHE_SIZE=128
***CONFIG_LITTLEFS_CACHE_SIZE=512 (default value)
```

> 当SPIFFS写满的时候，速度会急剧下降，如下是SPIFFS不同剩余内存写入时间
>
> ```
> 88000 bytes written in 1325371 us
> 88000 bytes written in 1327848 us
> 88000 bytes written in 5292095 us
> 88000 bytes written in 19191680 us
> 22784 bytes written in 74082963 us
> ```

## 读取588K的文件

```
FAT:          3,111,817 us
SPIFFS*:      3,392,886 us
LittleFS**:   3,425,796 us
LittleFS***:  3,210,140 us
*Only read 374,784 bytes instead of the benchmark 440,000, so this value is extrapolated
**CONFIG_LITTLEFS_CACHE_SIZE=128
***CONFIG_LITTLEFS_CACHE_SIZE=512 (default value)
```

## 删除588K的文件

```
FAT:         934,769 us
SPIFFS*:      822,730 us
LittleFS**:   31,502 us
LittleFS***:  20,063 us
*The 5th file was smaller, did not extrapolate value.
**CONFIG_LITTLEFS_CACHE_SIZE=128
***CONFIG_LITTLEFS_CACHE_SIZE=512 (default value)
```