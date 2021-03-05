---
title: WLAN从入门到精通-WiFi6
date: 2021-03-05 00:00:00
swiper: true
swiperImg: '/medias/3.jpg'
top: true
tags: WLAN
---

# Wi-Fi 6的前世今生

## 什么是Wi-Fi 6

说起Wi-Fi 6，其实是Wi-Fi联盟对IEEE的最新一代无线局域网标准802.11ax的命名。
这里面讲到了2个组织IEEE和Wi-Fi联盟。

首先介绍一下电气和电子工程师协会IEEE（Institute of Electrical and Electronics Engineers）。

![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151355ph5nyjtdkb8frbxt.png)

IEEE作为标准组织，通信人应该非常了解，有兴趣可以百度一下。在该组织制定的一系列标准中，通信人最熟悉802.3标准对应以太网，而属于无线局域网的就是802.11。早在1990年，IEEE就已经成了802.11工作组用来制定无线局域网的相关标准，并在1997年发布了第一个标准802.11-1997。之后的每4-5年，802.11标准就会升级换代一次，至今已有6代。

![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151043pqg616mmve8a3q1j.png)

而另一个组织，Wi-Fi联盟（英语全称Wi-Fi Alliance，简称WFA），其实是一个商业组织，这个联盟最初的目的是为了推动802.11b标准的制定，并在全球范围内推行Wi-Fi产品的兼容认证。兼容认证其实非常重要，因为802.11标准是很理论化，一旦产品化，每家厂商有可能会做的五花八门，所以Wi-Fi联盟所要解决的就是不同厂家的兼容性，此外Wi-Fi联盟还负责产品测试等工作。

![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/152808aef9ogfirof0r5hc.png)

我们现在熟悉的大部分手机厂商、部分运营商都是这个联盟的成员。
而我们常常说的Wi-Fi，实际上就是来自Wi-Fi联盟的商标。Wi-Fi联盟把符合802.11标准技术统一称为Wi-Fi。

2018年，为了方便记忆和理解，Wi-Fi联盟终于决定抛弃之前802.11n、802.11ac等专业标准名称，仿照移动通信中代际3G、4G、5G的划分，将现有标准简化为数字命名。

![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151136t23q4r4mx21b2tq3.png)

## Wi-Fi 6有多6

### 带宽6
过去每一代Wi-Fi的标准，一直致力于提升速率。
![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-5](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151155agcncgezdghaappp.png)
经过20多年的发展，Wi-Fi 6（802.11ax）在160MHz信道宽度下，理论最大速率已经达到9.6Gbps，是802.11b的近900倍。
而Wi-Fi 6速率的提升是因为采用了更高阶的1024-QAM、更多的子载波等技术。
### 并发6
Wi-Fi 6的优秀表现，不仅仅体现在速率的提升上，更主要体现在高密场景下的用户性能提升。
Wi-Fi 6引入了5G中的OFDMA和上行MU-MIMO等多用户技术，进一步提升了频谱利用率，使得Wi-Fi 6相比于Wi-Fi 5，并发用户数提升了**4倍**。
![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-6](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151218yltjwjnt116ry6gr.png)
### 时延6
在低时延场景，例如VR/AR-互动操作模拟、全景直播、互动式游戏、沉浸式会议、高清无线投屏等，Wi-Fi 5的30ms时延已经无法满足需求，而Wi-Fi因为引入了OFDMA和空间复用技术BSS Coloring，令时延降低至**20ms**。
![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-7](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151234uza8ce4hd7mvz8ha.png)
### 节能6
随着IoT设备广泛应用，除了提升终端速率外，Wi-Fi 6更是关注了终端的耗电情况。
Wi-Fi 6采用TWT技术，按需唤醒终端Wi-Fi，加上20MHz-Only技术，使得终端的功耗降低**30%**。
![【WLAN从入门到精通-Wi-Fi 6】第1期——Wi-Fi 6的前世今生-3375489-8](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/22/151248d0fxmtavpramxrty.png)

# 物善其用 OFDMA

无线和有线通信不同。有线通信中信息是承载在线缆上，通过高低电平来标识不同的信息010010。但是在无线通信中，信息的载体就变成了高频信号，这种将信息加载到某个高频信号进行传播的过程就叫做载波调制，而承载着信息的高频信号，我们也称之为载波。拿日常生活中的例子来说，Wi-Fi通信类似快递送货，载波就像是送货的小车，发送方通过载波调制将货物(信息)放到小车上再送达到接收方。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093931auljualk348u3gik.png)

当然在Wi-Fi中，载波也不是随意选择的，802.11协议详细定义了允许使用的频段，并精确划分出频率范围，每个频率范围就是我们所说的信道。下图展示了5GHz频段的信道资源。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093955s9t8nx2hs9cgcynh.png)

不过这些频段不仅Wi-Fi可以用，其他短距无线传输技术比如RFID、蓝牙等，也都可以使用，所以干扰也很多。
在Wi-Fi 4/5，载波调制用的是正交频分复用OFDM（Orthogonal Frequency Division Multiplexing）技术。

## 什么是OFDM

OFDM实际上是一种多载波技术，那为什么要用到多载波技术呢，其实主要还是为了克服Wi-Fi中的多径干扰。无线电波在传播过程中是向着各个方向传播的，在遇到不同障碍物的时候，会发生折射、反射，因此接收方可能会受到多个相同的电波，这就是多径干扰效应。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093956zjzl5n5c5q5eceg6.png)

多径干扰的会直接带来码间串扰。如下图所示，对于一组串行信号，由于信号的多径传输，接收端在收到两组相同的信号时，如果两组信号之间时延*t* 小于信号的传输间隔*T*，就会造成接收端在解析出正确是信号难度很大，这就是码间干扰。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093956mueh58mgmz2atmqd.png)

我们换一种思路，如果我们把这组信号一分为二，同样的时间，单个信号的传输间隔就可以拉开，而对于接收端而言，码间干扰对比上一种小很多。这就采用多载波调制可以减少多径干扰的原因。
前面已经介绍过了，802.11协议已经规定好了信道的频宽，为了能在有限的频宽下得到最多的没有干扰的载波（又称为“子载波”）就必须利用正交的特性来划分，因为正交的子载波之间既没有干扰又挨的足够近。
下图是802.11a/g下的子载波划分，20MHz信道被划分成64个子载波，子载波的间隔为312.5KHz。当然中间有部分子载波（例如边带保护子载波、导频子载波等）是有自己的用途的，可供传输与数据的子载波还是有48个。在802.11n/ac这个中数据子载波数量为52个，而在802.11ax中，20MHz被划分成256子载波，子载波的间隔为78.125KHz，数据子载波数量为234。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-5](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093956jlclc3txdwdvr2v0.png)

综上，OFDM可以将高速串行的数据，变成一组并行低速的数据，尽可能地减低了码间串扰。

## 为何引入OFDMA

然而OFDM也是有自己缺点的，因为在Wi-Fi 4/5中，通信都是单用户的，即每次发送数据，不管用户数据量大小，一个用户要全部占用全部子载波。我们把全部的子载波整体看成一辆送货的小车，如果用户的数据包很小，例如即时消息，浏览网页，用不了全部子载波，那么小车是装不满的，剩下闲置的子载波就浪费了。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-6](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093957ckn5tlh6nx1a67b8.png)

如何把这一部分闲置的子载波利用起来呢？为此Wi-Fi 6引入了5G中的正交频分多址OFDMA（Orthogonal Frequency Division Multiple Access）。OFDMA和OFDM虽然看起来差了一个字母，但OFDMA本质上是一种多址技术。
这里为大家科普一下，什么是多址技术，多址技术简单说就是一种区分不同用户的方法。无线通信中，常用的多址技术有频分多址FDMA、时分多址TDMA和码分多址CDMA。顾名思义，频分多址就是用不同的频率的信道来区分不同的用户；时分多址就是大家都用一个信道，但是把信道分成不同的时间片，用不同时间片区分不同用户；码分多址则是使用不通的码“码序列”来区分用户，每个用户都会分配一个独特的序列码，因此用户见也不会干扰。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-7](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093957s5v2dg4k7kgqpprv.png)

有个很经典的例子来讲清楚这3者的关系，假设一群人聚会，频分多址就是把会场分成不同的小房间，房间里面双方自然可以很清楚的听到对方讲话；时分多址，就是在这个小房间的所有人每人只能讲10分钟，这10分钟轮到你讲了，超过10分钟，就要给下一个人；码分多址，就是在场的人，使用不同的语言交流，打个比方****人，虽然背景叽叽喳喳英语、日语、韩语，但是因为两人只听得懂中国话，那些都可以作为噪声直接忽略。


好了，再说回OFDMA。OFDMA将信道划分成不同资源单元RU（Resource Unit），当然这些RU彼此之间都是正交的。在发送数据，不同的用户只会用某一个资源单元而非整个信道，这样就能实现一次向多个用户发送数据。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-8](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093957jnw85c5fm58msnmb.png)

还是拿小车来举例，OFDMA相当于在小车中划出专门的隔间，通过调度每个隔间放置不同用户的货物，这样一次可以为多个接收方送货。

![【WLAN从入门到精通-Wi-Fi 6】第2期——物善其用 OFDMA-3381321-9](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/28/093958hy2qj6spsqo4akzj.png)

总结来说，对OFDM而言，AP与每个用户都是单点通信的，如果需要跟3个用户通信，那就得3个周期；但是OFDMA则是点对多点通信的，一个周期就完成了与3个用户之间的通信，效率自然就高了。



# 万箭齐发 MU-MIMO

## 什么是MU-MIMO

Wi-Fi的多用户技术，不仅仅是OFDMA，还有早在Wi-Fi 5就引入的MU-MIMO。
在介绍MU-MIMO之前，稍微介绍一下与其颇有渊源的MIMO。
MIMO，即Multiple Input Multiple Output（多输入多输出）。如下图所示，MIMO技术可简单理解为将网络资源进行多重切割，然后经过多重天线进行同步传送。
![【WLAN从入门到精通-Wi-Fi 6】第3期——万箭齐发 MU-MIMO-3384461-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/31/101833hin4ifqb58eb7wgd.png)

MIMO带来的好处是增加单一设备的数据传输速度，同时不用额外占用频谱资源。可以说，从11g时代54Mbps的传输速率，到11n时代的300Mbps，甚至是600Mbps的传输速率，MIMO技术功不可没。

我们在WLAN设备上，常常会看到一个指标项*M*×*N* MIMO，也有写作*M* T *N* R的，这个指标项就是在讲天线数，M表示发送端的天线数，N表示接收端的天线数。在一个MIMO系统中，每一路独立处理的信号被定义为一路空间流（Spatial Stream），如果收发天线数量不相等，那么空间流数小于或等于收发端更小的天线数目。例如，4×4（4T4R）的MIMO系统可以用于传送4个或者更少的空间流，而3×2（3T2R）的MIMO系统可以传送2个或者1个空间流。

但是实际上，AP和终端的天线数是不对称的，AP的都是3或者4根天线，甚至更多，但是终端，比如手机，通常只有1-2根天线，这种不对称性会造成一部分网络资源的浪费。举例来说，目前支持4×4（每一条流的理论传输速率为433Mbps）802.11ac Wave 2的AP的整体理论传输速率可达1.732Gbps，当它与1×1（1天线）手机连接和传输时，最高理论传输速率仅为433Mbps，同一时间其余的1.3Gbps的容量都会被闲置，并且对MIMO而言，AP和终端之间通信都是单点的，也就是说，AP每次只能跟单个用户进行通信，其他用户只能等待，这也意味着单个终端的速率只会更低。
![【WLAN从入门到精通-Wi-Fi 6】第3期——万箭齐发 MU-MIMO-3384461-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/31/101833q3gbstjh7pi5r59r.png)
为此，Wi-Fi 5引入了MU-MIMO。MU-MIMO的MU就是多用户的意思，该技术意味着AP一次可以跟多个终端进行通信，这样就能充分利用AP的总容量。
还是拿送货作为比喻，试想一下，如果说Wi-Fi 4一趟只能开一辆小车送货给接收端，那么Wi-Fi 5/6就是一趟开多辆小车送货。
![【WLAN从入门到精通-Wi-Fi 6】第3期——万箭齐发 MU-MIMO-3384461-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/31/101833uvn32ejs4mk8d3hf.png)
早在Wi-Fi 5就已经支持了同时与4个用户进行下行通信，而Wi-Fi 6则同时支持上下行8个用户，因此用户的传输速率变得更高。
![【WLAN从入门到精通-Wi-Fi 6】第3期——万箭齐发 MU-MIMO-3384461-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/31/101834ctey8yjmqz798di0.png)
![【WLAN从入门到精通-Wi-Fi 6】第3期——万箭齐发 MU-MIMO-3384461-5](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/31/101834u6nqtqfabnthr0et.png)



## OFDMA和MU-MIMO的区别

MU-MIMO是一种多用户技术，而在前一篇中，我们曾经介绍过另一种多用户技术OFDMA。虽然OFDMA和MU-MIMO两者都是针对多用户，将串行变为并行，但其实两者还有很达差别的。
**MU-MIMO**：实现**物理空间**上的多路并发，适用于大数据包的并行传输（如视频、下载等应用），提升多空间流的利用率与系统容量，提高单用户的有效频宽，同样能降低时延。但运行状态不够稳定，很容易受终端影响。
**OFDMA**：实现**频域空间**的多路并发，适用于小数据包的并行传输（如网页浏览、即时消息等应用），提升单空间流的信道利用率与传输效率，减少应用延迟与用户排队。运行状态稳定，不容易受终端影响。
![【WLAN从入门到精通-Wi-Fi 6】第3期——万箭齐发 MU-MIMO-3384461-6](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202007/31/101834sp5byn46cz6ca1pn.png)
因此MU-MIMO和OFDMA两种方案完全不冲突，在实际使用中也经常是叠加使用。OFDMA与MU-MIMO联合调度可以基于每个业务进行资源分配（如网页浏览、视频观看、下载、即时消息等各类业务场景），通过设计合理MU-MIMO和OFDMA调度算法就能有效降低密集多用户情况下终端上下行随机接入造成的冲突，有效地改善多用户高密度接入场景的使用体验。



# 空间复用 BSS Coloring

## 软肋

无线通信不同于有线通信的另一特点，就是无线通信存在的**干扰问题**。无线通信的干扰是无处不在而且无法避免的。有线通信可以通过检测线缆上的高低电平识别到干扰，而无线通信无法进行这样的检测。因此802.11标准在MAC层设计了一种检测机制——载波侦听多址访问/冲突避免CSMA/CA（Carrier Sense Multiple Access /Collision Avoidance）。
CSMA/CA把在同**一个信道**上所有的站点想象成在一张圆桌上开会的人，大家本着先听后讲的原则，如果遇到有人发言就得等待一段时间（即退避），直到没人讲话才能开始发言。
![【WLAN从入门到精通-Wi-Fi 6】第4期——空间复用 BSS Coloring-3388145-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/04/144019z9eehaoye3lbjxex.png)
那如何判断是否有人发言呢？CSMA/CA提出了一种物理侦听的方式——CCA（空闲信道评估，Clear Channel Assessment），它通过检测信号的能量估计信道的忙闲状态。CCA使用两个门限判断信道是否空闲，**协议门限和能量门限**。想象一下很多人在一起聊天，**协议门限**用于检测是否有人发言，如果有，则其他人要等待当前发言人结束发言后才开始发言；**能量门限**用于检测环境是否太吵闹，如果环境很吵闹，发言也没有人能听得清，就要等到环境不吵闹时再发言。
在实际中，为了保证不错过发给自己的报文，一般都把协议门限设置的很低，但是这样干扰退避变的更容易发生，导致大家都在等，也就降低了整个无线系统的性能，这也是增加AP也不能扩展无线网络的网络容量的原因。

## 矛盾升级

随着用户数的激增，常常导致AP的部署也是非常密集的，这意味着AP可以侦听到其他所有同信道AP的帧。另一方面，信道是相对有限的，这些AP必然用到同一个信道。这就造成了这么一种场景，在某一个特定时间内，只有一个用户或者一个AP能够传输数据，哪怕他们根本不在一个区域。像是下图这种情况，AP1和AP2处于同一信道且可以彼此侦听到对方，虽然AP1和STA1的通信和AP2无关，但是AP1与AP2不能同时跟STA通信。
![【WLAN从入门到精通-Wi-Fi 6】第4期——空间复用 BSS Coloring-3388145-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/04/144020eteqbd584nr8bbqe.png)
为此，Wi-Fi 6进一步加强了空间复用技术，引入了BSS Coloring机制。

## 破局

BSS Coloring机制原理就是为不同的AP发出的报文套上不同颜色的信封，接收端收到信后，不拆信封就能立马判断是否跟自己相关，颜色相同表示跟自己相关，颜色不同就跟自己无关，对于跟自己无关报文就当不存在，接收端依旧可以发起通信而不必退避，这就达到了空间复用的效果。
![【WLAN从入门到精通-Wi-Fi 6】第4期——空间复用 BSS Coloring-3388145-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/04/144020oetrocbfrd7h8e91.png)
像上图所示，如果不标记颜色，只要AP都是用36信道，都会彼此干扰；但是如果标记了颜色，则认为只有同为黑色且使用36信道的AP，才会存在干扰，颜色不同就不会有干扰。

当然现实中BSS Coloring的实现要稍微复杂一点。颜色的标记是由WAC统一分配给AP，AP在报文头打上6 bit的颜色标记位，更准确的说是在PHY层和MAC层，这样不用解析报文就能判定是否跟自己相关。接收端收到报文后，如果颜色和关联AP的一样，就认定报文来自MYBSS，否则就是OBSS（Overlapping Basic Service Sets，重叠基本服务集）。区分出MYBSS和OBSS信号后，就可以双标对待了。Wi-Fi 6的做法是设置2个协议检测门限，MYBSS的协议门限可以尽量降低，这样可以尽量不错过来自MYBSS的报文；而对OBSS，OBSS的协议门限可以稍微高点，只要在OBSS的协议门限内，终端即认为不存在同频干扰，这样终端和AP依旧能发起通信，这就能达成了空间复用的效果。
![【WLAN从入门到精通-Wi-Fi 6】第4期——空间复用 BSS Coloring-3388145-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/04/144020ps2jsn03gksjj31k.png)

# 高阶调制 1024-QAM

## 什么是QAM

前面我们在讲OFDM时候，曾经介绍过载波调制。实际上，在载波调制前，还有一个环节，叫符号映射。符号映射就是将数字信号的bit映射为符号。
WLAN符号调制有BPSK、QPSK和QAM。
![【WLAN从入门到精通-Wi-Fi 6】第5期——高阶调制 1024-QAM-3389685-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/06/102113d1f226b22mp5ccqd.png)

最简单的BPSK和QPSK是一种相位调制，BPSK用0°和180°共2个相位表示0和1，即2种符号，传递1 bit的信息，参见上图；QPSK则使用0°、90°、180°和270°共4个相位，能够表示00、01、10和11共4种符号，传递2 bit的信息，所以其传输的信息量是BPSK的2倍。而Wi-Fi中采用的QAM（Quadrature Amplitude Modulation，正交幅度调制），是一种同时对相位和振幅的调制。下图是16-QAM的示意图。

![【WLAN从入门到精通-Wi-Fi 6】第5期——高阶调制 1024-QAM-3389685-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/06/102113j6q5tmoh3qc6wcyt.png)

为了方便理解和使用，QAM入了星座图。星座图上的每一个点都可以用一个夹角和该点到原点的距离表示。这里还是以16-QAM为例，星座图中这个夹角**φ**就是调制的相位，这个距离**A**就是调制的幅度。

![【WLAN从入门到精通-Wi-Fi 6】第5期——高阶调制 1024-QAM-3389685-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/06/102114dpptamj2k6xv4psg.png)

当然星座图中的点的**A**和**φ**也不是随意取的，为了在应用中不同的波形干扰最小以达到减少误码率的目的，所以这些点和相邻点之间的“距离”差不多，且是对称排布的。

## Wi-Fi 5和Wi-Fi 6的差别

在Wi-Fi 5中，采用的是256-QAM，1个符号表示8个bit；而Wi-Fi 6则采用了1024-QAM，也就是1个符号表示10个bit。

这就好比是货物打包，原本一个包裹只能携带8个bit信息，提高QAM的点数后，就可以携带10个bit信息。同样的一个包裹，比原先携带的内容多了，数据传输速率自然快了。

![【WLAN从入门到精通-Wi-Fi 6】第5期——高阶调制 1024-QAM-3389685-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/06/102115ufo4nt964ltw94k2.png)

有人要问，QAM点数能不能无线扩大呢？实际操作上是不能的。

因为发送一个符号所用的载波频宽是固定的，发送时长也是一定的，点数越多意味着两个符号之间差异就越小。这不仅对接收两方的器件要求很高，而且对环境的要求也很高。

下图以256-QAM为例，展示了不同的信噪比（SNR）环境下的星座图，可以明显的感受到，如果环境很嘈杂（SNR较小），则符号很容易因为命中相邻的其他点导致解调错误。

![【WLAN从入门到精通-Wi-Fi 6】第5期——高阶调制 1024-QAM-3389685-5](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/06/102115z0r32t00kkj5krki.png)

这就意味着，如果环境过于恶劣，终端将无法使用高阶的QAM模式通信，只能使用较低阶次的调制模式。

举个日常生活中的例子，两个人对话，如果彼此讲话速度很快，这就要求周围环境不能太吵，要是背景太嘈杂，显然也是听不清的，所以对话的双方只能放慢语速来保证对方能听清。



# 化零为整 前导码打孔

## 什么是信道绑定

之前曾经介绍过，Wi-Fi在传播的过程中，需要加载在高频信号上。这个高频信号并不是随便那一段都可以，而是由ITU-R （ITU Radio Communication Sector，国际通信联盟无线电通信局）定义的ISM（Industrial Scientific Medical，工业科学医学）频段，而且协议最初也规定了高频信号的宽度20MHz。

这20MHz的信道就好比是一条单车道的马路，马路只有这么宽，传输能力自然有限。想要提高传输能力，直接的做法是将马路拓宽。信道绑定的原理跟这个很类似。

![【WLAN从入门到精通-Wi-Fi 6】第6期——化零为整 前导码打孔-3396941-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/14/104716klk1kd0dipzo9r9u.png)

802.11n协议第一次引入**信道绑定**的概念，所谓信道绑定就是将相邻的2个20MHz信道绑定成一个40MHz信道来使用，这样数据传输速率能够成倍提高。而这2个信道，一个被称为主信道，另一个就是从属信道。主信道和从属信道的差别在于，主信道上会发送Beacon帧等管理报文而从属信道则不发送。所以主信道比从属信道要重要的多。
803.
当然，为了获得更大的传输带宽，Wi-Fi一直在努力。在802.11ac引入了80 MHz、160 MHz和80+80MHz带宽。

## 信道绑定并非无懈可击

有人要问，获得更高的传输带宽，是否是可以无限地将信道进行捆绑呢？很可惜不行。因为信道资源是有限的。从下图上就能看出，目前最大带宽——160MHz的信道，总共就2个。

![【WLAN从入门到精通-Wi-Fi 6】第6期——化零为整 前导码打孔-3396941-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/14/104716avv3lnocmop81inf.png)

这也是为什么会有非连续80+80MHz的信道出现的原因，因为连续的80 MHz信道太少。如果可以选择的信道太少，大家很容易都挤在一个信道上，冲突就会变得超大。

而且连续信道绑定机制也有自己的弊端。如果一个从属信道忙碌将会直接导致带宽降维。举个例子，在80M信道中，如果从属40MHz信道忙碌，那么只能变成20MHz信道，如果从属20MHz信道忙碌，那么直接降成20MHz信道。

![【WLAN从入门到精通-Wi-Fi 6】第6期——化零为整 前导码打孔-3396941-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/14/104716wv7hu2t0tod2q2i2.png)

这样显然对高密场景是很不利的，因为高密场景下部分信道会变得异常忙碌，WLAN设备很可能会一直工作在20MHz信道带宽模式下。

PS：有人问主信道忙怎么办？主信道忙那整个信道都不能用了，因为控制报文都在主信道上传输。

## 如何破解

为了解决这一个问题，802.11ax提出了前导码打孔（Preamble Puncturing）机制。这个机制的原理是，即使从属信道忙，传输带宽也不会下降，而是将剩下不连续的可用信道进行捆绑。还是以80MHz信道为例，从属20 MHz信道忙碌，依然可以利用主20 MHz和从属40 MHz信道的频谱资源进行数据发送，相比于只能使用主20 MHz信道的非前导码打孔模式，频谱利用率达到300%。

![【WLAN从入门到精通-Wi-Fi 6】第6期——化零为整 前导码打孔-3396941-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/14/104717fwc1bma8a4z0zi8a.png)

说一句题外话，为什么这个机制要叫前导码打孔？原因就是这种带宽模式的信息是需要发送端通过前导码携带给接收端的，而跳过的忙碌信道就好像打了一个孔一样。

# 以逸待劳 TWT

## 困境

节能是Wi-Fi 6考虑的另一个重要方面。

Wi-Fi 4/5其实也有节能模式，在一个通信周期内，终端会观察是否AP是否会向其发送数据，如果是，那么终端就保持苏醒，直到接收完成后，才会进入休眠模式。这其实对业务量很低的终端相当不公平，比如智能电表这种，可能久久才需要跟AP通信一次，但是大部分时间都要等待，这就造成了终端电量不必要的消耗。

![【WLAN从入门到精通-Wi-Fi 6】第7期——以逸待劳 TWT-3400161-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/18/140439xs9weiff1asgnirs.png)

## 解决方案

由此，Wi-Fi 6引入了TWT目标时间唤醒（Target Wake Time）机制，TWT是由802.11ah标准首次提出，TWT的初衷是针对IoT设备，特别是低业务量的设备而设计的一套节能机制。

![【WLAN从入门到精通-Wi-Fi 6】第7期——以逸待劳 TWT-3400161-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/18/140439d7dbgxaea5w264xd.png)

在TWT机制下，AP和终端可以建立一套TWT协议，双方约定好一个TWT服务时间，终端只有在服务时间内才会工作，其他时间处于休眠状态。

![【WLAN从入门到精通-Wi-Fi 6】第7期——以逸待劳 TWT-3400161-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/18/140439rsq5b7yhx9ij6lcj.png)

TWT有2种模式，一种是Individual TWT，一种是Broadcast TWT。

![【WLAN从入门到精通-Wi-Fi 6】第7期——以逸待劳 TWT-3400161-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/18/140440h6uug3u5gl62uto4.png)

Individual TWT，就是AP和终端一对一的协商TWT服务时间，每一个终端仅仅知道自己和AP协商的TWT时间，不需要知道其他终端的TWT时间。

一个一个协商实在太费事，于是Wi-Fi 6又新定义了一种Broadcast TWT。如果说，Individual TWT是私聊模式的话，Broadcast TWT就是群聊模式。Broadcast TWT由AP负责管理，在该机制下，TWT服务时间是由AP宣告，终端可以向AP申请加入Broadcast TWT，加组完成后，终端就可以获得AP的广播TWT服务时间了。

# 同与不同 Wi-Fi 6和5G

## 同

近年来，通信技术中热度最高的就是Wi-Fi 6和5G，很多人将二者视为竞争对手，认为总有一方会被淘汰。从蜂窝和Wi-Fi技术面世以来，类似的看法一直都存在。

![【WLAN从入门到精通-Wi-Fi 6】第8期——同与不同 Wi-Fi 6和5G-3403901-1](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/21/140931b6tu457q89mggewg.png)

确实两者在某种意义上是很相似的，都是致力于提供更高带宽、更多连接、更低时延的网络。
例如对大多数使用者来说，5G给我们最直观的感受是**速度快**，而Wi-Fi 6的速率也是快。

![【WLAN从入门到精通-Wi-Fi 6】第8期——同与不同 Wi-Fi 6和5G-3403901-2](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/21/140931s857rf7h9kub0z9j.png)

5G的速度有多快？Sub6G的单用户峰值速率在1.6 Gbps（四流），这也就意味着下载一部高清电影只需要一秒，Wi-Fi 6的单用户峰值速率也在1.2 Gbps（单流），两者的理论传输速率已经非常接近。

再比如两者的**核心技术**也是有**重叠**的。

| **项目** | **5G NR**                      | **Wi-Fi 6**     |
| -------- | ------------------------------ | --------------- |
| 多址技术 | OFDMA                          | OFDMA           |
| 调制方式 | 256-QAM                        | 1024-QAM        |
| MIMO     | SU：4流 MU：12流               | SU：8流 MU：8流 |
| 最大频宽 | Sub6G：100 MHz MMWave：400 MHz | 160 MHz         |
| 编码     | LDPC（数据）                   | LDPC            |
| 时延     | 1-10ms                         | 20ms            |
| 移动性   | 优                             | 差              |

## 不同

那么两者真的水火不容么？其实不然，Wi-Fi 6和5G之间的差异化，使得两者更像是互补的伙伴，相辅相成。

### 应用场景不同。

Wi-Fi 6因其使用的免费频谱资源和功率限制，而更加适合应用在室内。如果将其作为室外长距覆盖，则面临着很严重的干扰问题。

5G技术呢，使用的频段都是需要到相关机构申报的，所以干扰很小，但是由于使用的高频信号（24GHz-52GHz），极易衰减，因此一旦应用在室内场景，就需要相当复杂的规划。

![【WLAN从入门到精通-Wi-Fi 6】第8期——同与不同 Wi-Fi 6和5G-3403901-3](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/21/140932j706s20x4t66dmmr.png)

### 获得成本不同。

抛开两者使用的频段是否免费这一点不说，Wi-Fi 6和5G的搭建和使用成本也有所不同。根据中国信息通信研究院估算，由于初期5G设备成本较高，5G网络投资规模将是4G的2-3倍。除此以外，5G网络的部署还包括传输网、核心网。这直接导致5G套餐的资费成本比4G要高。同时5G需要专业的无线网络规划工程师，规划、运维成本都很高。Wi-Fi 6网络的部署简单，规划、运维不需要专业的工程师即可完成，而且一经部署则不再收取额外费用。

### 终端普及程度不同

目前一般新款手机都是同时支持Wi-Fi 6和5G，但是办公网络中的打印机、电子白板、智能楼宇控制系统、投影电视、智真系统等可能仅支持Wi-Fi连接功能，因此都会选择优先支持Wi-Fi 6网络的模式。

![【WLAN从入门到精通-Wi-Fi 6】第8期——同与不同 Wi-Fi 6和5G-3403901-4](https://forum.huawei.com/enterprise/zh/data/attachment/forum/202008/21/140932n93esjqteepj9pmj.png)

Wi-Fi 6和5G两者不同决定了他们不是**竞争关系**，而是**共存关系**，通过两者的相互协同，可以使整个接入网络的性价比达到最优。
