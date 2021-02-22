---
title: gdb
date: 2021-01-05 00:00:00
swiper: false
swiperImg: '/medias/5.jpg'
top: false
tags: 乐鑫
---

# 1、GDB RSP(GDB Remote Serial Protocol)

- 信息格式：$数据#校验码

- 多数信息使用ASCII码，数据由一系列的ASCII码组成，校验码是由两个16进制数组成的单字节校验码

- 接收方接收数据并校验，若正确则回应“＋”，否则回应“－”

## 1.1、请求

| 命令 | 描述           |
| :--: | :------------- |
|  ？  | 读当前系统状态 |
|  g   | 读所有寄存器   |
|  G   | 写所有寄存器   |
|  m   | 读内存         |
|  M   | 写内存         |
|  c   | 继续执行       |
|  s   | 单步执行       |
|  k   | 终止进程       |

## 1.2、答复

| 命令 | 描述                                           |
| :--: | :--------------------------------------------- |
|  “”  | 告诉GDB上次请求命令不支持                      |
|  E   | 告诉GDB出错                                    |
|  OK  | 上次请求正确                                   |
|  W   | 系统在exit_status状态下退出                    |
|  X   | 系统在signal信号下终止                         |
|  S   | 系统在signal信号下停止                         |
|  O   | 告诉GDB控制台输出（这也是唯一向GDB发出的命令） |

# 2、目标机上stub的实现

目标机上stub的基本功能是与主机GDB通信，实现读写内存、寄存器，stop、continue指令。主机GDB与目标机上stub通信的通用模型如图

![GDB与目标机上stub通信的通用模型.png](GDB与目标机上stub通信的通用模型.png)

![jtag-debugging-overview_zh.jpg](jtag-debugging-overview_zh.jpg)

# 3、GDB stub

- [OpenOCD support](https://github.com/projectgus/openocd)：需要单独的jtag
- barebones GDB stub ：仅支持异常捕获，同时要想在smart.js平台之外使用它，需要其他的工作，并且不支持FreeRTOS
- [esp GDB stub](https://github.com/espressif/esp-gdbstub)：支持裸机和FreeRTOS 
