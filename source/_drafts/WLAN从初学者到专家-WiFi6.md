# Wi-Fi 6之路

什么是Wi-Fi 6？

Wi-Fi 6是下一代Wi-Fi，也称为802.11ax。

关于Wi-Fi，您可能已经听说过两个主要组织：电气和电子工程师协会（IEEE）和Wi-Fi联盟（WFA）。

**电气工程师协会**

![image001.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/112730xew8nfk93tlkc4ww.png?image001.png)

通信技术领域的大多数专业人员都熟悉世界领先的标准开发者IEEE。其最著名的标准之一是IEEE 802.3-以太网。IEEE在1990年成立了802.11工作组来开发无线LAN（WLAN）标准，并在1997年发布了第一个WLAN 802.11标准。从那时起，IEEE每四到五年发布一次新标准，最新的标准是第六代802.11ax标准。
![image003.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/113611y1cguwd9d463xhen.png?image003.png)



**WFA**

WFA是一家商业组织，在全球范围内推广和销售802.11标准并认证802.11产品的互操作性。它以Wi-Fi CERTIFIED徽标认证符合802.11的技术。通过严格的测试，WFA会检查产品与各种配置下的其他经过Wi-Fi认证的产品之间的互操作性。802.11设备的大多数生产商都是WFA的成员，并且可以使用WFA拥有的Wi-Fi商标来标记其认证产品。
![image005.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/113740m9phuf1ucuxvswc6.png?image005.png)

在2018年，Wi-Fi联盟决定使WLAN标准更易于理解和记住。该组织以类似于移动通信中3G，4G和5G等不同世代的方式对它们进行了重命名。因此，802.11标准按代进行划分，最新的标准是Wi-Fi 6。
![image007.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/114012y0ea4app44y2p6jx.png?image007.png)

Wi-Fi 6的附加价值是什么？

**超级快**

在过去的20年中，Wi-Fi呈指数级增长。Wi-Fi 6利用各种“速度提升器”（例如高阶1024-QAM和更多子载波）在160 MHz信道宽度下提供9.6 Gbit / s的理论速度，比第一代Wi-Fi快900倍。

![image009.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/114044j7p9b2be7aqb25k1.png?image009.png)

**高并发**

Wi-Fi 6为小区域的高密度用户提供了出色的覆盖范围。为了支持高并发性，Wi-Fi 6引入了多用户技术，例如OFDMA和上行链路MU-MIMO，以提高频谱利用率。与Wi-Fi 5相比，这使Wi-Fi 6可以支持四倍的并发连接。



 

​         ![image011.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/114152vi4rpz3lmx3xt2fi.png?image011.png)

 

**超低延迟**

Wi-Fi 6引入了OFDMA和BSS着色，以将等待时间减少到20毫秒。与Wi-Fi 5（30毫秒延迟）不同，Wi-Fi 6满足了新兴应用程序（例如VR / AR，全景直播，互动游戏，沉浸式会议和高清无线投影）的延迟要求。

![image013.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/114357zu2a0oqy4y6x15qt.png?image013.png)

 **节约能源**

除了改善用户体验，Wi-Fi 6还解决了终端的使用问题。随着Wi-Fi终端数量的增加，其功耗也随之增加。Wi-Fi 6使用目标唤醒时间（TWT）通过按需唤醒终端来节省电量。TWT和仅20 MHz的技术共同节省了终端电池的30％寿命。

 ![image015.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/114529tmpbooqedtm6lroe.png?image015.png)

# OFDMA如何发挥作用

与通过电缆传输信息的有线通信不同，无线通信通过高频信号（也称为载波）承载信息。让我们想象一下Wi-Fi通信是一种快递，其中的信息就像货物一样，它们被装载到送货车上并运送到接收方。送货车就像是运载工具，将货物装载到搬运车上的过程类似于运载工具的调制。

![image_003](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/154324ax6na6ti20gxlxyg.png?image003.png)



IEEE 802.11定义了可用于载波的频带，并将每个频带精确地划分为几个频率范围，也称为信道。



![image005.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/154440nlza28q725v0coz7.png?image005.png)



上图显示了在5 GHz频带上提供的信道。Wi-Fi并非使用这些渠道的唯一技术；其他短距离无线传输技术（如RFID和蓝牙）也是如此。这样，严重的无线干扰是不可避免的。

发生这种情况是因为无线电波在所有方向上传播，然后被反射，折射或散射。在无线通信中，当发射器发出多个无线电波时，在接收器端会发生多径干扰。

 ![image007.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/154618tklsdrd69pxci60c.png?image007.png)



为了减少多径干扰，必须更有效地调制载波。

**OFDM：什么以及为什么？**

当一组串行数据信号（例如，下图中的信号1至6）通过多径传播到达接收器时，如果信号的传输延迟*t*大于其传输间隔*T，*则会发生符号间干扰（ISI）。ISI将使接收器难以正确解析信号。

![image009.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/154812xa58l5w1smwe77ew.png?image009.png)



多载波调制可以降低ISI。如果通过串行到并行转换将一组信号分成两组，则信号传输间隔*T*可以延长（*T* > *t*，如上图所示）。这将大大减少接收器上的ISI。

**正交频分复用（OFDM）**是Wi-Fi 4和Wi-Fi 5中采用的多载波调制技术，用于应对多径干扰和ISI。OFDM将一个信道划分为多个正交子信道，在这些子信道上调制转换后的并行信号。对应于正交子信道的载波通常称为子载波，它们不会互相干扰，从而使它们成为有限频率带宽下最干净（最无干扰）类型的子载波。

在实际的WLAN中，根据特定规则划分OFDM子载波。以802.11a / g为例，OFDM将20 MHz信道划分为64个子载波，间隔为312.5 kHz。在这些子载波中，有48个子载波发送数据，而其他子载波（例如DC，保护和导频子载波）专用于其他目的。

​         ![image011.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/154958i300i5wbwd5j0520.png?image011.png)

可用数据子载波的数量随802.11标准而变化。例如，在802.11n / ac中，有52个数据子载波。在802.11ax（Wi-Fi 6）中，有234个数据子载波（实际上，20 MHz信道被分成256个子载波，以78.125 kHz的间隔隔开）

综上所述，OFDM将高速串行数据信号转换为一组低速并行数据信号，从而使ISI最小化。

**为什么在Wi-Fi 6中使用OFDMA？**

如前所述，运输车就像是送货车。在OFDM中，无论大小如何，货车都会在每次行程中提供一个包裹。因此，通常浪费面包车中的一些空间。同样，即使仅发送一个数据包，即使它远小于载波容量，也将占用整个“面包车”，因此许多子载波会变得空闲。

![image013.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/155317mwlt2f6f00sfpobf.png?image013.png)



为了解决此问题，Wi-Fi 6引入了正交频分多址（OFDMA）以更好地利用子载波资源。

OFDMA本质上是一种多址技术。无线通信中的其他多址技术包括：

- 频分多址（FDMA）：将频率分为多个通道供用户使用。

- 时分多址（TDMA）：将信道划分为不同的时隙，在此期间每个用户都可以访问介质。

- 码分多址（CDMA）：通过分配唯一代码，可以在多个用户之间共享频谱。

     

     ![image015.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/155505iwfdwwkxn9l91w23.png?image015.png)

简而言之，FDMA将会议室分成较小的房间，人们可以清晰地听到彼此的声音。TDMA允许房间中的每个人都有特定的时间轮流交谈；CDMA允许人们使用不同的语言进行交流，而当每个人都在同时讲话时，人们只会选择他们能理解的语言，而会忽略他们不会理解的语言。

OFDMA是多路访问家族的成员，它将信道资源分成彼此正交的不同资源单元（RU）。这些RU被分配给不同的用户，这些RU携带各自的数据。这样，OFDMA可以实现并发多用户传输，从而避免了资源浪费。

![image017.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/155547j3leg1gq20557uy3.png?image017.png)

让我们回到我们的送货车类比。使用OFDMA，可将货车划分为多个隔室，以同时运送不同的包裹。该货车在单程中将多个包裹发送到不同的接收器，这比OFDM效率更高。

假设AP需要与三个用户进行通信：
- 使用OFDM，AP以点对点模式与每个用户通信，需要三个传输周期。
- 借助OFDMA，AP可以在一个传输周期内与所有用户实现点对多点通信。

![image019.png](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/12/155730xzyc6lu0lm0wc164.png?image019.png)



# MU-MIMO如何改变您的多用户体验

MU-MIMO是一种多用户技术，最早是作为Wi-Fi 5的一部分引入WLAN标准的。您可能会在上一篇文章中回想起这种多用户技术，在上一篇文章中我们讨论了OFDMA，目前是另一种多用户“玩家” Wi-Fi 6中提供了这两种技术。这两种技术都可以执行串行到并行转换，以在多用户场景中实现更有效的连接，并且可以在彼此互补的同时还展现出独特的特性，我们将在本文后面介绍。 。

但是首先，让我们在MU-MIMO上进行一些扩展。这里的重点是“ MIMO”，代表多输入多输出。因此，在深入研究MU-MIMO之前，我们必须首先了解MIMO的工作原理。

**什么是MIMO？**

下图说明了一个简单但典型的MIMO机制。网络资源在空间上进行了划分，因为使用了多个天线同时将数据传输到接收器端的多个天线。该技术极大地提高了单个设备的数据传输效率，而无需占用额外的频谱资源。

![1个](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/151703hacsihuspzt4n2ac.png?image008.png)

众所周知，MIMO巩固了其作为无线通信标准必不可少的组成部分的地位，极大地推动了Wi-Fi传输速度的提高，例如，从54 Mbps（802.11g）演变为300甚至600 Mbps（802.11n）。

WLAN设备的MIMO能力通常由*M* x *N* MIMO表示，其中*M*代表MIMO系统中的发射（Tx）天线和*N个*接收（Rx）天线。信号在Tx和Rx天线之间通过空间流传输；但是，可用空间流的数量取决于Tx或Rx天线的数量较少。例如，4x4（4T4R）MIMO系统最多支持4个空间流，而3x2（3T2R）MIMO系统仅支持1或2个空间流（因为只有两个Rx天线可用）。

要求WLAN上的AP和STA具有相同数量的天线是不切实际的，因为AP通常具有3或4，并且大多数STA（例如移动电话）只有1或2。因此，它们无法充分利用MIMO信道资源。例如，支持4x4 MIMO的802.11ac Wave AP提供的最大理论速率为1.732 Gbps。但是，当它与移动电话进行通信（1x1 MIMO）时，最大理论速率只能达到433 Mbps，因为它们之间只有一个空间流可用。在此示例中，未使用1.3 Gbps的速率。另外，AP一次只能与一个STA进行通信，要求其他AP等待轮流，从而进一步降低了单STA速率。

![2个](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/151750jr9ti4wtrztcwvrt.png?2.png)

 

**MU-MIMO如何弥合Wi-Fi中的MIMO差距？**

Wi-Fi 5引入了MU-MIMO来弥合此MIMO差距，因为该技术使AP可以同时与多个STA通信，从而充分利用AP的容量。

继续我们的送货车类比，可以将Wi-Fi 4看作是一趟每次将货物运送到一个接收器的货车。但是，Wi-Fi 5和Wi-Fi 6已将我们升级为货车车队，AP现在可以使用它们来将货物运送到多个接收者。

![3](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/151824wpi7xwt5b4k0je44.png?image012.png)

Wi-Fi 5和Wi-Fi 6每次旅行都支持不同数量的“货车”。在Wi-Fi 5中，AP最多可以同时与下行链路中的四个STA通信，而Wi-Fi 6允许在下行链路和上行链路中同时与多达八个STA同时通信，从而进一步提高了传输速率。

![3](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/151934p3vocm3vromnum4c.png?3.png)

![5](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/151850ztg6q7ez00i0zq67.png?image016.png)

 

**MU-MIMO和OFDMA：竞争还是互补？**

在Wi-Fi 6中，MU-MIMO和OFDMA作为互补的多用户技术运行。

**MU-MIMO：在物理上**划分网络资源，以提高视频流和下载等高带宽应用程序的容量和效率。MU-MIMO技术提高了空间流利用率和有效带宽，同时降低了延迟。然而，MIMO系统不够稳定，并且容易受到STA的影响。

**OFDMA：**支持**频域中的**多通道传输，非常适合诸如网络浏览和即时消息之类的低带宽，小数据包应用。OFDMA提高了空间流利用率和传输效率，同时减少了应用程序延迟和排队时间。此外，该技术稳定且对STA的影响具有弹性。

在典型情况下，这两种技术都可以相互补充，并在Wi-Fi网络上提供更高的资源利用率。例如，OFDMA + MU-MIMO联合调度可根据服务（例如Web浏览，视频流，下载和即时消息传递）优化分配资源。通过适当的算法设计，此联合调度模式可减少由随机STA访问引起的冲突，并在人口稠密的场景中增强用户体验。

![6](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/151907o2fmwhxv7uo7v2mu.png?image018.png)



# BSS着色和空间复用

无线通信与有线通信的不同之处在于，无线通信中的干扰是常见且不可避免的。为了减轻这种干扰，802.11引入了MAC层检测机制-载波侦听多路访问和冲突避免（CSMA / CA）。

**困难处理干扰**

从概念上讲，CSMA / CA会将某个信道上的所有STA视为某种“无线电传输圆桌会议”的参与者。STA必须先收听，然后轮到他们讲话，然后再讲话，以确保在开始传输之前它们不会在无线电环境中打扰其他人。

![1个](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/153254oh5sezeebzvlgbr3.png?1.png)

但是参与者如何确定何时发言？CSMA / CA使用明信道评估（CCA）机制来检测以下各项：

1.有人在说话吗？

2.环境太吵了吗？

CCA将这些分别实现为**信号检测**（SD）阈值和**能量检测**（ED）阈值。通常将STA配置为低SD阈值，以避免由于干扰而丢失任何帧。但是，这将使STA倾向于退后并等待。如果过多的STA退后等待，可能会对系统性能产生严重影响。这也解释了为什么我们不能仅仅部署更多的AP来扩展WLAN。

随着Wi-Fi网络上STA的数量猛增，高密度AP部署已成为常态。不幸的是，可以使用的通道数量有限，并且许多这些AP最终可能会在同一通道上工作。当同一信道上的AP靠在一起时，它们可以互相干扰，例如下图中的AP1和AP2。

![2个](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/153326kmv10eee6sd0mvee.png?image010.png)

AP1和AP2位于不同的基本服务集（BSS）中，但在同一信道上工作，争用信道资源来传输数据。有时，该信道将仅由一个AP或一个STA（STA1或STA2）占用，这意味着AP1和AP2无法与其关联的STA同时进行通信，即使AP位于不同的BSS中。这浪费了频谱资源并且大大降低了传输效率。幸运的是，这是BSS着色可以提供帮助的地方。

**Wi-Fi 6利用BSS着色实现更好的空间重用**

BSS着色允许Wi-Fi 6网络用颜色标记信道，类似于将来自不同AP的帧密封在不同颜色的信封中。在接收到这种信封中的帧后，接收者可以直接确定它是来自相同的BSS还是来自不同的BSS，而无需打开信封。如果帧来自内部BSS，则位于“我的” BSS（MYBSS）中；如果来自内部BSS，则它位于重叠的BSS（OBSS）中。如果帧是MYBSS帧，则接收方将不会费心启动新的传输，从而大大减少了不必要的退避，从而可以更好地利用频谱资源

下图显示了BSS着色如何帮助消除高密度AP部署中的干扰。只要同一信道上的AP用不同的颜色标记，它们之间就不会相互干扰，例如，黑色信道36上的AP和灰色信道36上的AP可以同时传输数据。

![3](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/153440chx9ihx4kkllfqt7.png?3.png)

在Wi-Fi网络上实现BSS着色比看起来要困难得多。它依赖于6位BSS颜色字段，该字段在PHY和MAC层均交换。WLAN AC将该字段放在发往AP的帧的头中，以便它们可以将MYBSS帧与OBSS帧区分开。与仅定义一个CCA SD阈值的Wi-Fi 4或5不同，Wi-Fi 6对每种帧类型定义的SD阈值不同：

- 对于MYBSS帧，将阈值设置为尽可能低的值，以便可以毫无损失地接收MYBSS帧。

- 对于OBSS帧，将阈值设置得较高。只要信号RSSI在指定的阈值之内，STA就会认为该信道是无干扰的并且可以进行通信。

![4](https://forum.huawei.com/enterprise/en/data/attachment/forum/202008/22/153451r2hihxqgiutidyj5.png?4.png)

 

Wi-Fi 6与效率有关。BSS着色可通过减少同信道干扰并优化拥挤场所中的传输效率来改善空间复用，从而最大化Wi-Fi网络性能。



# 1024-QAM高阶调制

**什么是QAM？**

正交幅度调制（QAM）是一种先进的调制方案，广泛用于数字电信系统中。例如，在802.11 Wi-Fi标准中，在载波调制之前，将数字位映射到符号。可以使用二进制相移键控（BPSK），正交相移键控（QPSK）或QAM来调制这些符号。BPSK和QPSK都通过改变符号的相位来调制符号，而QAM则将相位调制和幅度调制结合在一起。

BPSK使用0°和180°相位来传输单个信息位（0或1），如下图所示。相比之下，QPSK可以通过以下四个阶段对每个符号（00、01、10或11）编码2位：0°，90°，180°和270°。这使QPSK可以承载的信息量是BPSK的两倍。

![003](https://forum.huawei.com/enterprise/en/data/attachment/forum/202009/01/101509vb8bywpvzmkl6ubp.png?image003.png) 

QAM通常由星座图（也称为星座图）表示，其点对称分布在正方形网格中，以最大程度地减少不同波形之间的干扰。网格中的星座点数通常为2的幂，并且等于QAM阶数。由于该图是正方形，因此常见的QAM格式为16-QAM，64-QAM，256-QAM等。该图显示了16个QAM将符号调制成的16个波形。

![002](https://forum.huawei.com/enterprise/en/data/attachment/forum/202009/01/101509l0ugc5o91te1agm9.png?image005.png) 

在星座图上，星座点的角度（φ）表示相位调制，并且从0,0到星座点的距离的变化表示幅度修改。

 ![001](https://forum.huawei.com/enterprise/en/data/attachment/forum/202009/01/101507hclzl3grfrf97s9l.png?image007.png)

**QAM如何与Wi-Fi配合使用（Wi-Fi 5与Wi-Fi 6）？**

与Wi-Fi 5相比，Wi-Fi 6引入了广泛的技术创新，从而带来了更高的数据速率。1024-QAM（一种高阶调制方案）就是这样一种创新。具体来说，Wi-Fi 5的256-QAM代表1个8位符号，而1024-QAM代表1个符号最多10位。通过每个符号携带更多的比特，可以大大提高数据速率。如果我们将每个符号视为一个框，则高阶调制方案使我们可以将更多信息塞入每个框。

 ![004](https://forum.huawei.com/enterprise/en/data/attachment/forum/202009/01/101509zasriojn7r3sb77u.png?image009.png)

但是，QAM订单不仅是“多多益善”的情况。在某些传输周期和载波带宽条件下，可能会密集放置大量星座点，并且在这种情况下可能很难识别单个符号。这对接收器的组件和环境提出了很高的要求。例如，随着在256 QAM环境中信噪比（SNR）降低，这些符号难以解调，并且容易出现解调错误。

 ![005](https://forum.huawei.com/enterprise/en/data/attachment/forum/202009/01/101509lxeflvq0flff3qek.png?image011.png)

结果，在这种“嘈杂”的环境中，低阶QAM调制方案是唯一的选择。换句话说，如果我们在嘈杂的环境中讲得太快，个别单词可能会被淹没。为了确保每个单词都被清楚地理解，我们必须放慢脚步。

