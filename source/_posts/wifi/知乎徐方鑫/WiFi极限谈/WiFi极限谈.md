---
title: WiFi极限谈
swiper: false
swiperImg: /medias/16.jpg
top: false
tags: WLAN
categories: wifi6
abbrlink: 7074a8fd
date: 2021-03-15 00:00:00
---

###### WiFi极限谈

# 最大传输距离的“标准”答案

## 序言

看到有不少讨论Wi-Fi技术极限的内容，可能大家对于Wi-Fi的极限能力一直都比较有兴趣。所以我在这里整理一些Wi-Fi极限的“标准”答案，顾名思义，这里的标准答案并不是现实里面Wi-Fi到底能实现到什么程度，这个随着技术进步实际上一直都在增加，属于没有一个定式的答案。我们这里说的“标准”答案，就是从Wi-Fi协议，就是802.11协议里面，目前可以推导出的答案，也就是802.11协议的理论极限值。

本文我们就首先探讨一下Wi-Fi的最大传输距离，我们的主要关注点是协议的MAC层，关于物理层的部分，实际上可以有很多技术提升传输距离，比如说天线，波束，接收信号放大等等，实际上可操作性的余地比较高，主要还是成本因素限制，而MAC层更多是规则，而且是固化到IC中的，所以无法修改。所以我们的关注点在MAC机制上。

## ACK Timeout与远距离Wi-Fi

我们知道一个802.11的传输过程首先是要Backoff，然后竞争到信道以后，传输DATA并接收ACK，如下图所示

![img](https://pic1.zhimg.com/80/v2-aeed87db578ed680102f5a1e172270b4_720w.jpg)

远距离Wi-Fi一般都是点对点传输的，所以没有竞争的问题，所以Backoff过程中并没有太多影响传输距离的因素。

**主要因素在ACK上**：标准的802.11协议中，当发送者发送完Data（如上图Packet A）之后，等待SIFS的时间。**在这SIFS时间内需要接收到对端的ACK，否则就会认为ACK Timeout，需要进行重传。**

这里SIFS与协议版本有关，参考Wiki上的时间总结，我们先不关注802.11ah/ad，因为工作模式和标准的802.11有所区别。先关注标准的802.11，比如a/b/g/n/ac。

*Remark：802.11ah/ad的内容可以参考最后的计算方式自行计算，本文讨论的主要是802.11的主体协议。*

![img](https://pic4.zhimg.com/80/v2-39ab98a2ed6ccdd077a481fb8e86d5e7_720w.jpg)

上表中，我们以802.11ac的SIFS做一个计算距离，我们这里假设无线信号在空气中的电磁波传播速率为299792458m/s。

那么802.11ac的理论最远距离就是**299792458m/s \* 16us = 4.7967km**了**。**

如果超越了这个时间，那么就会因为距离的传播延迟，接收不到对端的ACK，从而无法正确传输。

这种无法正确传输在一些不需要高保真的内容传输下，问题还局限于性能低下。如果接收方能够成功接收数据，但是发送方由于时延无法接收到ACK的话，那么发送方会重传7次，最后丢包，然后传下一个数据帧。虽然对性能造成影响，但是起码还能够传输。但是如果是一些高保真的内容，在802.11以外做了ACK要求的话，那么就有可能会出现不断重传的死循环了。

那么上面的计算是不是最终值呢？给出这个问题答案肯定就不是了。我们前面是以SIFS作为示例，描述了**传播时延（也就是距离因素）**对802.11的MAC层传输造成的影响，在802.11协议中，就有**传播时延（Air Propagation Time）**这个参数，所以我们下面关注一下。

## Air Propagation Time

在802.11中就有Air Propagation Time这个参数，该参数与Slot的大小和SIFS都有关系，通常在协议中，我们默认该参数是<<1us，因为1us的传播延迟大约覆盖为200m左右的空间，而Wi-Fi的覆盖范围也是这个大小的。

该参数在协议中的含义为：距离最远的节点与节点，其两者之间的最大传播时延。

![img](https://pic3.zhimg.com/80/v2-b1186cb05d36bdf6aa7d023c50b89f76_720w.png)

但是在802.11协议中，在Country information element这一个参数中，有一个Coverage Class field，该参数实际上是可以设定Air Propagation Time的。如下图所示：

![img](https://pic1.zhimg.com/80/v2-54caeee46341e162830e14dccb2a01dc_720w.jpg)

我们可以在该图表中发现，在802.11协议的制定的时候，就有设置出对于不同覆盖范围，采用不同的Air Propagation Time的设置。

在上表中，最大传播时延是93us，那么我们现在可以计算：

**802.11的理论最大距离**为**299792458m/s \* 93us = 278.8040km**。

从而我们就计算出了标准下的最大传输距离了。

## Air Propagation Time与SIFS

然而，我们前面也说了，这是协议中的最远距离的“标准”答案。但是这一块由于实际应用非常稀少，所以协议一直都存在一个bug（笔者自己认为）。

在DCF一篇《[802.11协议精读2：DCF与CSMA/CA](https://zhuanlan.zhihu.com/p/20721272)2》中，我们已经说明Slot和SIFS的构成了。

aSIFSTime= aRXRFDelay（射频延迟）＋aRXPLCPDelay（物理层头部接收延迟）＋aMACProcessingDelay（MAC层处理延迟） + aRxTxTurnaroundTime（发送接收天线转换时间）

aSlotTime= aCCATime（CCA时间）＋aRxTxTurnaroundTime（发送接收天线转换时间）＋aAirPropagationTime（传播延迟）＋aMACProcessingDelay（MAC层处理延迟）

这里需要注意的是，SIFS中没有考虑到Air Propagation Time，这就是和我们前面说的ACK Timeout问题不符合了。这里ACK Timeout的问题挖掘出来是在现实里面，10Km距离点对点部署的时候发现的，而估计标准中并没有考虑到这一点。

Slot时间是根据前面说的Coverage Class field动态调节aAirPropagationTime，进而调节Slot时间的，参考如下：

![img](https://pic4.zhimg.com/80/v2-b4a88096989bde465f4afb74d2df52e3_720w.jpg)

标准中在Slot内部考虑Air Propagation Time的原因是在于backoff counter的同步倒数，大家都是按照同一个时间间隙后退的，否则就会造成不公平。所以在Slot内有这样一个设计，而SIFS的内容就没有过多设想。这一块在标准的更新中，基本长年是没有变化的。按照道理，SIFS内也应该考虑到Air Propagation Time。

不过目前，这一切在协议中说法还算是自洽的，在FH物理层部分Air Propagation Time的描述中

![img](https://pic2.zhimg.com/80/v2-891e8420ba5203db81331b6172704211_720w.png)

我们可以看到，Air Propagation Time和SIFS的时间约束关系协议还是认知的，这句话的来源实际上是最早1997版本的802.11协议了，一直保留到现在。

在后来的802.11协议中，实际上这点就很弱化了，比如下面的定义

![img](https://pic3.zhimg.com/80/v2-01dca0aeb8812fb1d3edf4f596ced33e_720w.png)

关于802.11ah里面，SIFS时间是160us，不过其中Air Propagation Time考虑的时间是6us，实际上也是满足SIFS的要求的。

综上，我们讨论了在802.11协议中，标准给出的最大传输距离的答案，仅仅可以作为一个理论的参考。实际情况下，这么远的距离还无法做到，或者说，即使可以用Wi-Fi做，也没有要用Wi-Fi的必要性了，完全可以跑别的协议。

# 最大连接数的“标准”答案

## 序言

关于Wi-Fi路由最大能够连接多少个节点，也是Wi-Fi常常讨论的一个问题。这个答案也众说纷纭。本文就关注于一个Wi-Fi路由究竟能够连接多少个节点，给出“标准”中的答案。不过，“标准”的答案实际上是协议的上限，但是在现实场景中，是远远达不到该上限的，据我目前的了解，在实际上的路由产品中，目前最高的产品可能能带到100台左右的设备，但是也并不是跑纯的CSMA./CA的工作模式了，需要引入很多中心调度的设计。

## Associate过程

802.11的路由和节点连接的时候，首先要进行关联操作。关联操作具体如下：

![img](https://pic2.zhimg.com/80/v2-aceaaa46064799c34b9a7252006bfd89_720w.jpg)

上图是参考权威指南里面的图。在802.11的基础架构工作模式下（也就是家用路由器工作的模式），节点需要连接上AP，必然要经历Assocation的过程。这个过程包含两个部分。

**1）**节点需要向AP发送关联请求，Associateion Request。

![img](https://pic3.zhimg.com/80/v2-4ce1e265d3c87cf4f8b0129ef691656a_720w.png)Associateion Request

Associateion Request的帧结构如上，这个是一个典型的管理帧。用户需要向指定AP进行关联（即指定SSID），并且需要跟AP告知一些自己的工作能力（Capability Info等），这是信息是节点有义务告知AP，AP会利用这一些信息完成该BSS内的配置，比如说是否要开启兼容模式（参考前802.11b/g兼容的文章）。并且AP还需要去判断，该节点是否能够关联到本BSS下，如果节点的速率不适配，或者其他的原因（比如说用户自定义做的限制），那么节点会被拒绝连接。

2）AP在接收到节点的关联请求后，需要反馈Associateion Response。

![img](https://pic4.zhimg.com/80/v2-b9e940e3a4b78b9220c3c6d39cd6368f_720w.png)Associateion Response

无论AP是允许节点加入到该BSS，还是拒绝，都需要反馈Associateion Response。成功还是失败会在Status Code里面标明。并且AP会反馈一些AP认同的参数给节点（Capability Info等），可以认为节点发送给AP的是希望的参数，但是最终节点能够采用什么参数，还是要看AP的反馈。

这里我们另外需要关注的就是**Associateion ID**。Associateion ID简称**AID**，这个是节点加入BSS后，AP给节点所取的一个别名。我们知道节点有MAC地址，即BSSID。但是在很多环境下，比如为每一个节点分配缓存，那么就需要设置一个索引index。这个index如果用BSSID来设置的话，那么会太长，而且比较复杂。所以AP需要为节点取一个别名。AID的序号是AP决定的，一般情况下默认从0开始，根据节点的关联顺序依次加一。

这里记录一点抓包记录

**关联过程**：

![img](https://pic1.zhimg.com/80/v2-39acd419479f9867a35f20296e5db3a8_720w.png)

**Association Request**：

![img](https://pic3.zhimg.com/80/v2-c002f7b8a53e8e5f9ff6186819fb672a_720w.jpg)

**Associate Response**：

![img](https://pic4.zhimg.com/80/v2-34a09ae5b843b15472908266f8d6ff1f_720w.jpg)

以上的内容我们需要知道，在Wi-Fi工作模式下，每一个节点都需要被分配一个AID，所以AID的数目上限实际上就是Wi-Fi路由的“标准”最大关联数。

## Duration/ID（AID字段）

AID的限制实际上是出现在Duration/ID字段中，该field是一个复用的field，在一个标准的MAC层头部中都包含Duration字段。

![img](https://pic2.zhimg.com/80/v2-e1f9244a786559d2a5f178c9a6e6d8d9_720w.jpg)标准MAC帧结构

Duration字段全称是Duration/ID字段，在协议中有三种用法：

![img](https://pic1.zhimg.com/80/v2-a5050444186ad62dc5a14d1078b6f1ec_720w.jpg)Duration/ID

**用法1**：用作NAV设置，其包含15位，所以大小是从0到2^15-1=32767。

**用法2**：用法2实际上也是NAV，被用作在CFP（即PCF）模式下。此时第15位会被设置为1。为什么要设置这样的NAV最大值（即最大值为32768），需要理解倒数第二位是一个标志位，如果设置为1，那么是标志的意义，所以在解析成NAV的时候，其不能够设置为1。这也是为什么Duration字段为什么是16位，但是最大值只有32768，是标志位的原因，不是有符号整形的原因。

**用法3**：当14位和15为被设置为1的时候，此时Duration/ID就被认为是AID字段。这种用法一般出现在PS-Poll帧中。

![img](https://pic1.zhimg.com/80/v2-02e1d10fddf3abc36a4758a31641da98_720w.jpg)PS-Poll

可以看到在PS-Poll帧中，该字段就直接会被解析成AID字段。工作机理可以参考前面写过的《[802.11协议精读10：节能模式（PSM）](https://zhuanlan.zhihu.com/p/21623985)》。

## AID的数量限制

本节我们主要就关注一下AID的限制数目，对应前面说的，最大连接数的标准答案就是AID的最大数目。我们看一下协议中对于AID字段的解析：

在**802.11ac以前**（包含802.11ac）的版本中，该字段解析如下：

![img](https://pic4.zhimg.com/80/v2-1ff9e29a28e2e42ed4f68f84c885049b_720w.jpg)

我们可以看到，AID的范围是1-2007，虽然AID有14位，但是有很多高位是用作保留位不使用的。这里不适用主要是为了以后扩展功能。就好比我们知道，IP地址和MAC地址都有组播的区分的，所以AID将来也可能会有这样的功能，所以要设置保留位。

然后我们看下在**802.11ax**中（即**Wi-Fi 6**）中的字段解析：

![img](https://pic3.zhimg.com/80/v2-62ee828fddf9174911d690d1eb7ccb8e_720w.jpg)

Wi-Fi 6的OFDMA接入过程中，Trigger帧里面为每一个RU分配接入节点也是通过AID参数完成的。由于Wi-Fi 6还支持OFDMA下的随机接入UORA，所以需要设置特殊的AID字段。所以协议中就设置了两个特殊的AID，即2045和2046，还有AID=0的时候赋予了新的意义。这里相关意义可以参考《[Wi-Fi 6(802.11ax)解析17：UORA上行随机接入机制（UL-OFDMA）](https://zhuanlan.zhihu.com/p/77528922)》。

前面描述的常用802.11标准下的AID范围，我们现在可以得出一个答案，就是标准802.11的场景下，**最大理论连接数实际上限是2007**。

但是802.11还有一个“大连接”的版本802.11ah，802.11ah的设计目标和NB-IoT，5G下的mMTC（Massive Machine Type Communications）场景目标是一样的。都是支持大规模的节点数关联。所以我们还有必要关注下**802.11ah（即Wi-Fi Hallow）的AID范围**。

![img](https://pic2.zhimg.com/80/v2-f403ffd7377a2c04d12c8cafa1546089_720w.jpg)

这里我们看出来，802.11ah将AID的范围从2007扩展到8191，但是这个范围不是用前面说的PS-Poll帧，而是S1G PS-Poll帧，S1G是sub-1G的意思。即802.11ah的场景下，最大连接数是8191。

## 结语

我们总结下前面的最大连接数**，在802.11的主体标准中，“标准”最大的理论连接数是2007，不过在802.11ah这一个专门为大连接优化的协议中，最大理论连接数是8191**。

实际上这个数值还是非常大的，现实中我们目前不太可能发现一个路由能够挂2007个节点。实际上据我目前所了解到，单个AP最大连接数应该是可以做到100左右的，但是能够做到100左右的都不是像家用路由这样单纯的跑DCF（即CSMA/CA）的，单纯跑CSMA/CA的话能到30,40就已经非常不错了，跑到100左右的其实都是跑调度，类似PCF的一种TDMA工作模式。802.11协议其实是提供很多调度接口的，实际上每一次协议演进 时候，最大关联用户数实际上是不断增加的，所以其设置2007这个数字，确实需要设置大一些，确保协议还有发展空间。

另外，不仅仅在无线的Wi-Fi场景下有最大理论用户上限，实际上早期的支持CSMA/CD的repeaters也是由于协议有最大客户端数限制的，所以由于协议导致的极限并不是第一次出现，如下图即是在《Ethernet: The Definitive Guide》中所述（注：第416页）：

![img](https://pic2.zhimg.com/80/86ee7ca322842234dcd67112e4cbba75_720w.jpg)

那么基于CSMA/CD的repeaters，最大用户数目是1024，该1024实际上是基于CSMA/CD的BEB最大回退10次所导致（第0~10次会指数增加回退窗口，第11~16次不增加），而若采用包交换的交换机，则没有这个限制。

