---
title: HE信道访问26.2
tags: WLAN
categories: wifi6
abbrlink: cceb2019
date: 2021-04-29 00:00:00
---

# 基于TXOP持续时间的RTS / CTS

在HE BSS中，RTS/CTS基于TXOP 持续时间（duration）或者PSDU长度，对于非AP HE STA，AP可以将其配置为基于TXOP Duration的RTS/CTS交换来降低密集环境中的干扰。

HE AP发送的HE operation element中，将HE operation parameters字段中的TXOP Duration RTS Threshold子字段设置为1~1022，设置STA基于TXOP duration RTS/CTS交换。设置为1023，则禁用。设置为0，则保持不变。

如果与AP关联的STA收到非零的TXOP Duration RTS Threshold，则STA必须将dot11TXOPDurationRTSThreshold设置为收到的值，否则保持不变。

如果启用了基于TXOP Duration 的RTS/CTS，则需要满足：

- STA给AP发送单独寻址的帧
- TXOP Duration大于等于 32us * dot11TXOPDurationRTSThreshold

# Intra-BSS and inter-BSS PPDU 分类

STA接收到的PPDU满足下述任意一个条件就是inter-BSS PPDU

- RXVECTOR中参数BSS_COLOR不为0，并且不是STA所属BSS的颜色
- 如果PPDU是VHT PPDU，其RXVECTOR参数PARTIAL_AID不等于与STA关联的BSS的BSSID[39:47]，或者在相同的多个BSSID集或共同托管的BSSID中的其他BSS的BSSID[39:47]，并且RXVECTOR中参数GROUP_ID为0（0或者63代表是一个VHT SU PPDU，否则代表VHT MU PPDU）
- 如果PPDU是VHT PPDU，其RXVECTOR参数PARTIAL_AID不等于dot11PartialBSSColorImplemented为真的STA所属的BSS宣布的颜色的4 LSBs，同时，当最新HE Operation element中的Partial BSS Color字段为1时，RXVECTOR参数的GROUP_ID等于63
- 如果PPDU是VHT MU PPDU或者HE MU PPDU，RXVECTOR参数中UPLINIK_FLAG等于0，并且这个STA是一个AP

