---
title: coredump
date: 2021-01-05 00:00:00
swiper: false
swiperImg: '/medias/5.jpg'
top: false
tags: 乐鑫
---

# UART 、ELF FORMAT 、CRC32

| text | data | bss  | dec  | hex  | filename           |
| ---- | ---- | ---- | ---- | ---- | ------------------ |
| 2417 | 112  | 1512 | 4041 | fc9  | core_dump_port.o   |
| 821  | 0    | 0    | 821  | 335  | core_dump_flash.o  |
| 241  | 0    | 0    | 241  | f1   | core_dump_common.o |
| 1304 | 0    | 0    | 1304 | 518  | core_dump_uart.o   |
| 7913 | 0    | 388  | 8301 | 206d | core_dump_elf.o    |

| text  | data | bss  | dec   |
| ----- | ---- | ---- | ----- |
| 12696 | 112  | 1900 | 14708 |

# UART 、BIN FORMAT、CRC32

| text | data | bss  | dec  | hex  | filename           |
| ---- | ---- | ---- | ---- | ---- | ------------------ |
| 2417 | 112  | 1512 | 4041 | fc9  | core_dump_port.o   |
| 821  | 0    | 0    | 821  | 335  | core_dump_flash.o  |
| 2874 | 0    | 256  | 3130 | c3a  | core_dump_common.o |
| 1304 | 0    | 0    | 1304 | 518  | core_dump_uart.o   |
| 0    | 0    | 0    | 0    | 0    | core_dump_elf.o    |
| 7416 | 112  | 1768 | 9296 |      | SUM                |

| text | data | bss  | dec  |
| ---- | ---- | ---- | ---- |
| 7416 | 112  | 1768 | 9296 |

# UART 、ELF FORMAT 、SHA256

| text | data | bss  | dec  | hex  | filename           |
| ---- | ---- | ---- | ---- | ---- | ------------------ |
| 2834 | 112  | 1512 | 4458 | fc9  | core_dump_port.o   |
| 1079 | 0    | 0    | 1079 | 437  | core_dump_flash.o  |
| 241  | 0    | 0    | 241  | f1   | core_dump_common.o |
| 1343 | 0    | 0    | 1343 | 53f  | core_dump_uart.o   |
| 7913 | 0    | 388  | 8301 | 206d | core_dump_elf.o    |

| text  | data | bss  | dec   |
| ----- | ---- | ---- | ----- |
| 13410 | 112  | 1900 | 15422 |

# FLASH、ELF FORMAT 、CRC32

| text | data | bss  | dec  | hex  | filename           |
| ---- | ---- | ---- | ---- | ---- | ------------------ |
| 2444 | 112  | 1512 | 4068 | fe4  | core_dump_port.o   |
| 3767 | 0    | 80   | 3847 | f07  | core_dump_flash.o  |
| 241  | 0    | 0    | 241  | f1   | core_dump_common.o |
| 1343 | 0    | 0    | 1343 | 53f  | core_dump_uart.o   |
| 7913 | 0    | 388  | 8301 | 206d | core_dump_elf.o    |

| text  | data | bss  | dec   |
| ----- | ---- | ---- | ----- |
| 15617 | 112  | 1980 | 17709 |

# FLASH、BIN FORMAT 、CRC32

| text | data | bss  | dec  | hex  | filename           |
| ---- | ---- | ---- | ---- | ---- | ------------------ |
| 2444 | 112  | 1512 | 4068 | fe4  | core_dump_port.o   |
| 3767 | 0    | 80   | 3847 | f07  | core_dump_flash.o  |
| 2874 | 0    | 256  | 3130 | c3a  | core_dump_common.o |
| 0    | 0    | 0    | 0    | 0    | core_dump_uart.o   |
| 0    | 0    | 0    | 0    | 0    | core_dump_elf.o    |

| text  | data | bss  | dec   |
| ----- | ---- | ---- | ----- |
| 15617 | 112  | 1980 | 17709 |

# FLASH、ELF FORMAT 、SHA256

| text | data | bss  | dec  | hex  | filename           |
| ---- | ---- | ---- | ---- | ---- | ------------------ |
| 2861 | 112  | 1512 | 4485 | 1185 | core_dump_port.o   |
| 4025 | 0    | 220  | 4245 | 1095 | core_dump_flash.o  |
| 241  | 0    | 0    | 241  | f1   | core_dump_common.o |
| 0    | 0    | 0    | 0    | 0    | core_dump_uart.o   |
| 7913 | 0    | 388  | 8301 | 206d | core_dump_elf.o    |

| text  | data | bss  | dec   |
| ----- | ---- | ---- | ----- |
| 15040 | 112  | 2120 | 17272 |

# 依赖

panic

sha256

crc32

esp自己实现的几个task相关的系统函数

flash 加密

partition

编译链相关