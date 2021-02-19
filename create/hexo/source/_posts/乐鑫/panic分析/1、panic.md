---
title: panic
date: 2021-01-05 00:00:00

# 文章出处名称 #
from: 灵溪驿站

# 文章出处链接 #
url: www.creekwater.cn

# 文章作者名称 #
author: creekwater

# 文章作者签名 #
about: 当生活心怀歹毒地将一切都搞成了黑色幽默，我顺水推舟把自己变成了一个受过高等教育的流氓。

# 文章作者头像 #
avatar: https://www.notes.worstone.cn/image/hexo.png

# 是否开启评论 #
comments: true

# 文章标签 #
tags: ESP

# 文章分类 #
categories: ESP

# 文章摘要 #
description: ESP32 panic流程分析
---

# 1. dport_panic_highint_hdl.S

call4 panicHandler：传递(XtExcFrame *)frame

# 2. panic_handler

- 保存frame：
  ```c
  g_exc_frames[core_id] = frame
  ```
  
- 确保只有一个内核进入panic

- 如果是PANIC_RSN_INTWDT_CPU0引起的，正在执行代码的CPU必须是CPU0

- 如果是PANIC_RSN_INTWDT_CPU1引起的，正在执行代码的CPU必须是CPU1

- 如果是PANIC_RSN_CACHEERR引起的，引起错误的CPU不是正在执行代码的CPU，则不打印，并清掉g_exc_frames[core_id]

- 使能cpu cache

- 如果是PANIC_RSN_INTWDT_CPU0/1引起的，需要重启看门狗

- 将架构异常信息frame（XtExcFrame）转换为抽象的紧急信息info（panic_info_t）

- 打印info

***引起异常的原因***

> PANIC_RSN_DEBUGEXCEPTION			
>
> PANIC_RSN_DOUBLEEXCEPTION		  
>
> PANIC_RSN_KERNELEXCEPTION			
>
> PANIC_RSN_COPROCEXCEPTION		  
>
> PANIC_RSN_INTWDT_CPU0					
>
> PANIC_RSN_INTWDT_CPU1					
>
> PANIC_RSN_CACHEERR						  

# 3. frame_to_panic_info:异常信息转换

- 初始化
  ```c
  typedef struct {
    int core;                               // 引发异常的核心
    panic_exception_t exception;            // 引发异常的原因
    const char* reason;                     // 异常字符串
    const char* description;                // 异常的简短描述
    panic_info_dump_fn_t details;           // 有关异常的更多信息
    panic_info_dump_fn_t state;             // 处理器状态，通常是寄存器的内容
    const void* addr;                       // 触发异常的指令地址
    const void* frame;                      // 参考框架，直接从汇编传过来的
    bool pseudo_excause;                    // 表示异常原因有特殊含义的标志
} panic_info_t;

  info->core = cpu_hal_get_core_id();
  info->exception = PANIC_EXCEPTION_FAULT;
  info->details = NULL;   
  info->reason = "Unknown";
  info->pseudo_excause = pseudo_excause;    //这里是true
  
  info->state = print_state;
  info->frame = frame;
  ```
  
- 异常信息格式转换
  ```c
  if (pseudo_excause) {
      panic_soc_fill_info(frame, info);
  } else {
      panic_arch_fill_info(frame, info);
  }
  ```

## 3.1 panic_soc_fill_info：信息格式转换

- CPU0看门狗引起的异常
  
  ```
  frame->exccause == PANIC_RSN_INTWDT_CPU0
  ```
  
  填充：
  
  ```c
  info->core = 0;
  info->exception = PANIC_EXCEPTION_IWDT;
  info->reason = "Interrupt wdt timeout on CPU0"
  ```
  
- CPU1看门狗引起的异常
  
  ```c
  frame->exccause == PANIC_RSN_INTWDT_CPU1
  ```
  
  填充：
  
  ```c
  info->core = 1;
  info->exception = PANIC_EXCEPTION_IWDT;
  info->reason = "Interrupt wdt timeout on CPU1"
  ```
  
- cache错误引起的异常
  ```c
  frame->exccause == PANIC_RSN_CACHEERR
  ```
  
  填充：
  
  ```c
  info->core =  esp_cache_err_get_cpuid();
  ```

# 4. esp_panic_handler

- 打印panic info的信息
- 如果panic看门狗未启动，则配置RTC看门狗

- 关闭IWDT，启动TWDT，配置1秒后重置，留时间打印调试信息

- 关闭IWDT、TWDT，将panic info写入flash或者uart
- 关闭RTC看门狗
- 设置重启原因，重启系统（或者关闭所有看门狗，进入死循环）

# 5. 默认打印，无关乎写入flash或者uart

### 5.1 串口默认显示

```shell
Guru Meditation Error of type StoreProhibited occurred on core  0. Exception was unhandled.
Register dump:
PC      : 0x400f360d  PS      : 0x00060330  A0      : 0x800dbf56  A1      : 0x3ffb7e00
A2      : 0x3ffb136c  A3      : 0x00000005  A4      : 0x00000000  A5      : 0x00000000
A6      : 0x00000000  A7      : 0x00000080  A8      : 0x00000000  A9      : 0x3ffb7dd0
A10     : 0x00000003  A11     : 0x00060f23  A12     : 0x00060f20  A13     : 0x3ffba6d0
A14     : 0x00000047  A15     : 0x0000000f  SAR     : 0x00000019  EXCCAUSE: 0x0000001d
EXCVADDR: 0x00000000  LBEG    : 0x4000c46c  LEND    : 0x4000c477  LCOUNT  : 0x00000000

Backtrace: 0x400f360d:0x3ffb7e00 0x400dbf56:0x3ffb7e20 0x400dbf5e:0x3ffb7e40 0x400dbf82:0x3ffb7e60 0x400d071d:0x3ffb7e90
```

### 5.2 IDF监视器进行补充

```shell
Guru Meditation Error of type StoreProhibited occurred on core  0. Exception was unhandled.
Register dump:
PC      : 0x400f360d  PS      : 0x00060330  A0      : 0x800dbf56  A1      : 0x3ffb7e00
0x400f360d: do_something_to_crash at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:57
(inlined by) inner_dont_crash at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:52
A2      : 0x3ffb136c  A3      : 0x00000005  A4      : 0x00000000  A5      : 0x00000000
A6      : 0x00000000  A7      : 0x00000080  A8      : 0x00000000  A9      : 0x3ffb7dd0
A10     : 0x00000003  A11     : 0x00060f23  A12     : 0x00060f20  A13     : 0x3ffba6d0
A14     : 0x00000047  A15     : 0x0000000f  SAR     : 0x00000019  EXCCAUSE: 0x0000001d
EXCVADDR: 0x00000000  LBEG    : 0x4000c46c  LEND    : 0x4000c477  LCOUNT  : 0x00000000

Backtrace: 0x400f360d:0x3ffb7e00 0x400dbf56:0x3ffb7e20 0x400dbf5e:0x3ffb7e40 0x400dbf82:0x3ffb7e60 0x400d071d:0x3ffb7e90
0x400f360d: do_something_to_crash at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:57
(inlined by) inner_dont_crash at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:52
0x400dbf56: still_dont_crash at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:47
0x400dbf5e: dont_crash at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:42
0x400dbf82: app_main at /home/gus/esp/32/idf/examples/get-started/hello_world/main/./hello_world_main.c:33
0x400d071d: main_task at /home/gus/esp/32/idf/components/esp32/./cpu_start.c:254
```

