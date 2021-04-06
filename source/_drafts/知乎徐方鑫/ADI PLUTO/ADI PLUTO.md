---
title: ADI PLUTO
swiper: false
swiperImg: /medias/9.jpg
top: false
tags: WLAN
categories: wifi6
abbrlink: ef1afa25
date: 2021-03-15 00:00:00
---

###### ADI PLUTO

# 基本介绍

## **序言**

随着IC的技术发展，软件无线电（Software Defined Radio，SDR）已经从原来非常昂贵的价格，逐渐面向民用和学生的学习。

目前价格最实惠的一款就是今天介绍的ADI Pluto。ADI Pluto是ADI公司推出的主动学习模块（Active Learning Module），其主要包含三个设备：ADALM1000，ADALM2000，**ADALM-PLUTO。**其中前两个设备偏向基本的电路测量，ADALM-PLUTO偏向软件无线电。

![img](https://pic2.zhimg.com/80/v2-2005d8f9e76c0294b90004e2707b1751_720w.jpg)

在ADI给出的技术路线上，也表示该设备是适用于高年级的本科生，硕士研究生以及博士在读学生进行相关的技术学习的。目前设备的价格已经从早前的$99提升到了$149美元，但是从SDR的设备上而言，这个价格还是比较适合入门，而且也有很大的竞争力的。

*PS：ADI Pluto目前国内有两个版本，一个是原版的，一个是国行和seeed合作的。后者会贵一点，不过配置是和前面的一样的。笔者两个版本都买过。前者可以在digikey买，后者是淘宝。*

## **ADI Pluto的结构**

Pluto是一款基于FPGA的SDR架构，主要由5个部分组成，如下图所示：

![img](https://pic2.zhimg.com/80/v2-07523f48d1f92b07c5b3e7a7f5f176e1_720w.jpg)

实际上是一个Zynq的FPGA+AD936X的封装版本，我们所看到的很多商业应用的SDR版本也是基于该平台的。不过直接入手FPGA和AD9361开发，相对于很多开发者而言，都是比较困难的，而且针对于算法开发和学习，也是没有太大必要的。Pluto的固件也是开源的，[https://github.com/analogdevicesinc/plutosdr-fw](https://link.zhihu.com/?target=https%3A//github.com/analogdevicesinc/plutosdr-fw)，不过要比较专业的开发人员才会用到。所以ADI这一块的Pluto设备，确实是一个不错的学习平台。

![img](https://pic1.zhimg.com/80/v2-143319b921beb7c0e8a830769449b4e4_720w.jpg)

如上图所示，实际上其核心是ADI的AD9363和Xilinx Zynq。那么为什么觉得Pluto这个学习平台不错呢，主要是因为其开发时，不需要进行FPGA开发，可以通过ADI开发的Libiio组件进行调用。

![img](https://pic1.zhimg.com/80/v2-1501b2579e5325deb49e697f9ced7a14_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-cb4e2a33e52051af8e230169833b206d_720w.jpg)

如上图所示通过LibIIO组件，可以直接调用底层的SDR资源，从而方便开发很多。上层可以用C，Matlab等开发语言和平台进行二次开发。关于ADI Pluto的一些IIO实现细节，本文我们不展开。

具体一些产品参数如下：

![img](https://pic2.zhimg.com/80/v2-b8ed70825b1310513d351a1014788081_720w.jpg)

不过这个平台还是建议大家学习，或者做学术demo用，不适合做开发。因为PA和LNA太弱，信号质量没有那么好。如果做开发还需要额外的做信号放大，反而麻烦一些。

## IIO Oscilloscope

Pluto一开始拿到手之后，一般先做一个连接，看看设备能不能工作。由于一般都是直接连接到windows系统下面，所以我们采用IIO Oscilloscope做测试。

IIO Oscilloscope软件的下载地址：[https://wiki.analog.com/resources/tools-software/linux-software/iio_oscilloscope](https://link.zhihu.com/?target=https%3A//wiki.analog.com/resources/tools-software/linux-software/iio_oscilloscope)。

![img](https://pic4.zhimg.com/80/v2-210afb2f4ffc4238dd44ac5d9bb5f41f_720w.jpg)

装好IIO Oscilloscope启动，并且连接设备后，我们在上面就可以看到设备了

![img](https://pic1.zhimg.com/80/v2-38fa3dafa567cc419d6f0b2620a95998_720w.jpg)

刷新一下，可以看到更多细节信息

![img](https://pic3.zhimg.com/80/v2-a1c4bd7c878b54537ba303bb765a8fda_720w.jpg)

把信号源选一下，然后启动，看到波形就说明设备连接上了。

# GNURADIO安装（直接安装）

## 序言

前面我们简单介绍了ADI Pluto，本文我们安装下通常的开发平台，GNURadio。GNURadio有两种安装方法，本文我们介绍第一种安装方法，直接安装。

本文参考如下：

- [Analog Devices Wiki](https://link.zhihu.com/?target=https%3A//wiki.analog.com/resources/tools-software/linux-software/gnuradio)
- [https://www.eevblog.com/forum/rf-microwave/adalm-pluto-sdr/](https://link.zhihu.com/?target=https%3A//www.eevblog.com/forum/rf-microwave/adalm-pluto-sdr/)

## 安装步骤（直接安装）

第一步：将需要的补丁以及GNURadio都用apt-get的形式进行安装

Install GNU Radio and other dependencies

```text
apt-get -y install gnuradio-dev libxml2 libxml2-dev bison flex cmake git libaio-dev libboost-all-dev swig
```

然后分别源码下载libiio，libad9361，gr-iio。

第二步：Download and build libiio

```text
git clone https://github.com/analogdevicesinc/libiio.git
cd libiio
cmake .
make 
sudo make install
cd ..
```

第三步：Download and build libad9361-iio

```text
git clone https://github.com/analogdevicesinc/libad9361-iio.git
cd libad9361-iio
cmake .
make 
sudo make install
cd ..
```

第四步：Download and build gr-iio

```text
git clone https://github.com/analogdevicesinc/gr-iio.git
cd gr-iio
cmake .
make 
sudo make install
cd ..
sudo ldconfig
```

以上完成后就成功安装完成。

## Demo测试

这些都安装完了之后，可以进行测试，测试是否成功安装了整个程序

![img](https://pic1.zhimg.com/80/v2-cfdeb8617d835d70cb6a1e0d46755ddc_720w.jpg)

显示出如下结果即为成功

![img](https://pic1.zhimg.com/80/v2-df7a3e44de2b9f42544dff56ed93cbe4_720w.jpg)

PlutoSDR控件可以在Industrial IO下面找到

![img](https://pic3.zhimg.com/80/v2-3a2a8b70c2d7d18309e013e91458ac52_720w.png)

QT GUI Frequency Sink和QT GUI Waterfall Sink可以在Core->Instrumentation下面找到

![img](https://pic2.zhimg.com/80/v2-bc1d7098fb0801a88cf08583261c6a59_720w.jpg)

另外值得一提的是PlutoSDR Source的设置

![img](https://pic4.zhimg.com/80/v2-36d7dfe20c2ab577951fc78de20695e7_720w.jpg)

这里Device URI是根据ip地址来设置的，也就是说PC和Pluto是以网络接口互通的。该IP地址是Pluto本身预设的，如果要改可以通过修改Pluto存储中的config.txt文件。

![img](https://pic4.zhimg.com/80/v2-0b23fb189cfdea99148ebc062bc5835f_720w.jpg)

其实还存在另外一种连接的思路，也就是直接通过usb进行连接，不过笔者测试一直都没有成功，可能是libiio驱动升级之类的原因，可能不是推荐这样连接了吧。按照理论而言，我们可以通过iio_info查看到pluto的挂载信息。

![img](https://pic1.zhimg.com/80/v2-c4d2c621236ebc1fb01aae4cce60895c_720w.png)

理论上连接的时候，填写usb:1.2.5也可以，不过笔者这一点没有成功。所以目前还是按照IP连接的方式吧。

# GNURADIO安装（PyBomb）

## 序言

前面我们简单介绍了ADI Pluto，本文我们安装下通常的开发平台，GNURadio。GNURadio有还有另外一种安装形式，通过PyBomb安装，本文简单介绍下。

本文参考如下：

- [Analog Devices Wiki](https://link.zhihu.com/?target=https%3A//wiki.analog.com/resources/tools-software/linux-software/gnuradio)
- [ADALM-PLUTO SDR Hack: Tune 70 MHz to 6 GHz and GQRX Install](https://link.zhihu.com/?target=https%3A//www.rtl-sdr.com/adalm-pluto-sdr-hack-tune-70-mhz-to-6-ghz-and-gqrx-install/)
- [https://archive.fosdem.org/2018/schedule/event/plutosdr/attachments/slides/2503/export/events/attachments/plutosdr/slides/2503/pluto_stupid_tricks.pdf](https://link.zhihu.com/?target=https%3A//archive.fosdem.org/2018/schedule/event/plutosdr/attachments/slides/2503/export/events/attachments/plutosdr/slides/2503/pluto_stupid_tricks.pdf)
- [https://github.com/gnuradio/pybombs](https://link.zhihu.com/?target=https%3A//github.com/gnuradio/pybombs)
- [https://www.eevblog.com/forum/rf-microwave/adalm-pluto-sdr/](https://link.zhihu.com/?target=https%3A//www.eevblog.com/forum/rf-microwave/adalm-pluto-sdr/)

## 安装步骤（PyBomb安装）

首先要安装pybomb

```text
sudo pip install PyBOMBS
```

如果要更新或者安装最新版本的Pybomb的话，那么可以执行下面的指令

```text
sudo pip install --upgrade git+https://github.com/gnuradio/pybombs.git
```

首先配置auto-config

```text
pybombs auto-config
```

添加 PyBOMBS recipes

```text
pybombs recipes add-defaults
```

一般情况下默认即可，或者手动指定最新的recipes

```text
pybombs recipes add gr-recipes git+https://github.com/gnuradio/gr-recipes.git  
pybombs recipes add gr-etcetera git+https://github.com/gnuradio/gr-etcetera.git
```

配置prefix目录并且安装GNURadio

```text
pybombs prefix init ~/prefix -a myprefix -R gnuradio-default
```

然后要安装libiio，libad9361，gr-iio。（可以安装的组件可以这里找到：[gnuradio/gr-recipes](https://link.zhihu.com/?target=https%3A//github.com/gnuradio/gr-recipes)）

```text
pybombs install libiio
pybombs install libad9361
pybombs install gr-iio
```

然后每一次运行的时候，要设置一次环境变量，否则指令会找不到

```text
source ~/prefix/setup_env.sh
```

最后运行GNURadio

```text
gnuradio-companion
```

## **开机自动设置环境变量**

把source命令写入/etc/profile文件，这样每次开机都会自动设置环境变量。

即把source ~/prefix/setup_env.sh这个写入到profile最后即可

然后demo测试下即可，demo测试可以参考前面一篇：[ADI PLUTO 2：GNURADIO安装（直接安装）](https://zhuanlan.zhihu.com/p/56131512)。

# PothosSDR（Windows平台SDR软件全家桶）

## 序言

PothosSDR实际上是windows下SDR开发的全家桶了，本来PothosSDR最适配的硬件应该是LimeSDR，不过目前大部分组件Pluto都可以挂的上了，所以很方便。本文开始就开始对于PothosSDR做一个介绍。

## PothosSDR介绍

PothosSDR其是一个开发组件，包含了很多个具体的软件，当我们安装PothosSDR之后，可以看到桌面上多了这几个安装的软件：

- **CubicSDR**
- **GNURadio Companion**
- **GQRX SDR**
- **Inspectrum**
- **Pothos Flow**
- **Zadig v2.4**
- **Lime Suite**

其中大部分都是SDR开发常用的，比如说GNURadio和Pothos Flow，其他还有一些好用的软件，比如CubicSDR和GQRX SDR，然后可以播放存储波形的Inspectrum，剩下来的还有一个Zadig是用来安装SDR设备驱动的，以及具体可以对LimeSDR寄存器设置的Lime Suite，不过最后两个笔者用的是Pluto SDR，所以用不上。前面几个用处还是很大的。

![img](https://pic1.zhimg.com/80/v2-cc431e5f28bb972fd20a1bfcbf4abb10_720w.jpg)

那么具体的PothosSDR的生态如上图，图中我们要注意一个**SoapySDR**的组件，这个算是PothosSDR的核心组件了，比如Pothos Flow在连接Pluto的时候，就是通过SoapySDR，而不是直接通过libiio连接的了。

## PothosSDR的下载安装

PothosSDR的下载安装基本上没难度，笔者这里备份了一个下载文件为：[PothosSDR](https://link.zhihu.com/?target=https%3A//download.csdn.net/download/fzxy002763/12304223)。

官方下载地址为：[PothosSDR](https://link.zhihu.com/?target=https%3A//downloads.myriadrf.org/builds/PothosSDR/)。

然后安装就按照默认就行了，需要注意一点就是PATH这里的选项，记得要选一下

![img](https://pic1.zhimg.com/80/v2-bd30467cc06eb5fcbc864745a19f4808_720w.jpg)

安装完之后，我们可以在桌面上看到已经安装的几个软件。不过有一些软件还需要再继续配置安装，比如说GNURadio，启动的时候会执行安装脚本。我们后面会分解每一个软件的具体使用。

安装成功就可以看到桌面多了几个图标

![img](https://pic4.zhimg.com/80/v2-f9766871bc94cc33f131348d8351dba3_720w.jpg)

不过有几个可能没有默认图标的，所以看到这几个图标出来就可以了。

# GNURadio（Windows平台）

## 序言

前面有单独列举Linux下面如何安装GNURadio的，在Windows下面实际上也是可以直接安装GNURadio平台并进行开发的，只不过很多开源程序make的时候比较折腾，所以笔者本身不怎么用windows平台做GNURadio。在Windows下面安装GNURadio，我们是沿着前一篇PothosSDR的安装继续做的，以PothosSDR的安装是最简单的，不要单独去裸装GNURadio。

## GNURadio（Windows平台）

windows下面安装实际上大部分是在跑脚本，当我们安装完PothosSDR后，桌面会出现

![img](https://pic1.zhimg.com/80/v2-fcc96d978689f3dbcd0ea4bbf9c8330c_720w.jpg)

GNURadio的图标，这时候双击打开就会自动跑脚本了。

中间可能会用到的一些安装组件我打包了一下：[GNURadio（windows组件）](https://link.zhihu.com/?target=https%3A//download.csdn.net/download/fzxy002763/12304239)。

具体安装开始时候有可能会有两个地方错误：

- Python 2.7的安装，需要提前把Python 2.7安装好，否则的话会报错，打不开图标
- GTK+的安装，默认的GTK+我试验的是需要手动安装好的，否则GNURadio的安装脚本会卡在那里不动，安装的时候需要注意安装64位的GTK+，然后跑脚本的时候安装的是pyGTK了。

GTK+的错误如下图所示：

![img](https://pic1.zhimg.com/80/v2-bda2fcc9119d2f80494e47cc38761c4c_720w.jpg)

手动安装完GTK+之后就能继续往下跑了，如下图开始一个一个下载然后pip安装组件。这里的话还是pip的常见问题，因为pip服务器在外网，所以速率会比较慢，大家可以通过代理的方式提升下载速率。这里除了下载失败，其他应该不会有什么问题了，如果一直是下载失败，那么只有手动安装whl文件了，安装过程我就不补充了，相应的文件我放在前面的补充文件中了。

![img](https://pic3.zhimg.com/80/v2-62bf4b965986f72326015f887304bafa_720w.jpg)

安装搞定以后，重启启动GNURadio就可以进去了。

因为GNURadio可以直接连接Pluto，所以比较简单，不用关联到SoapySDR的配置。具体使用和Linux下面是一样的，可以参考前面的文章搭一个简单的场景做测试：

![img](https://pic4.zhimg.com/80/v2-09979ad45f6de259390eb80bcbc8944f_720w.jpg)

具体可能麻烦点的就是找组件，前面已经描述过了《[ADI PLUTO 2：GNURADIO安装（直接安装）](https://zhuanlan.zhihu.com/p/56131512)》，以上是在Windows下GNURadio的安装了。

# PothosFlow（Windows平台）

## 序言

PothosFlow实际上是类似于GNURadio的一个开发平台，笔者也说不上两个到底有什么区别，大致可以说GNURadio的信号处理函数更多，但是要说开发上手以后的框架结构的话，可能是PothosFlow更好一些（但是上手折腾）。另外一点偏实用主义一些的话，就是PothosFlow上面可以跑LoRa，该软件下面有直接集成的控件，所以实现LoRa更合适一些。本文我就简单介绍下PothosFlow。

## PothosFlow

PothosFlow的运行直接打开就行了

![img](https://pic4.zhimg.com/80/v2-e32208b6d2bc79ebd7277363d76520a3_720w.png)

运行之后可以看到整个界面实际上很类似GNURadio，如下

![img](https://pic1.zhimg.com/80/v2-0b305af8fccd833a929398181be8c8b4_720w.jpg)

可以搭一个如下图的demo

![img](https://pic1.zhimg.com/80/v2-9f8903e03767d52500dadcd32a0f6ca8_720w.jpg)

不过PothosFlow入门上手倒是挺折腾的，这一部分基本上没什么人写过。入门Demo的核心实际上是Soapy SDR组件的调用，当新拖拽一个Soapy SDR Source的组件（该组件在Source下面）后，其默认是这样的

![img](https://pic1.zhimg.com/80/v2-6bb58e151abc683802317c268cb44b14_720w.jpg)

右边的device name要自己填，另外下面还会报很奇怪的错误。这个错误笔者一直都没搞明白是哪里错了，花了很多时间调研。不过后来发现，这个错误类似于Warning，所以不影响我们设备的连接。

接下来就是要知道这个device name怎么写了，这里默认的是JSON的结构，那么device name需要查看SoapySDR里面具体对应的设备名，用如下指令

```text
SoapySDRUtil.exe --find
```

![img](https://pic4.zhimg.com/80/v2-167033a0af8d054d3cb167aa4c65b063_720w.jpg)

上图中，我们可以看出来，Pluto对应的设备名就是plutosdr。另外用下面的指令我们也可以查找。

```text
SoapySDRUtil --probe="driver=plutosdr,hostname=192.168.2.1"
```

![img](https://pic3.zhimg.com/80/v2-dbc6ba1d8535632f3201b7fe24c72aea_720w.jpg)

当找到设备名后，我们把Soapy SDR Source进行配置。

![img](https://pic1.zhimg.com/80/v2-1d1d4a422fd85600799067634012dcd0_720w.jpg)

配置成功时候，需要注意会有命令行的显示，如下

![img](https://pic4.zhimg.com/80/v2-53e7453e2d51c1cf357abb87d2d13f77_720w.png)

然后就是需要具体设置一些参数，比如说要看2.4GHz频段的信号，那么首先要在Soapy SDR Source里面定义Freqency为2.4e6，另外需要着重注意的是，**Gain Mode**需要进行设置**，**要不然手动调节Gain Value的大小，或者把Gain Mode设置为Automatic，否则的话，输出的瀑布图就为0，实际上就会是显示不了瀑布图。

![img](https://pic4.zhimg.com/80/v2-78d40dec386b7ecff371f488e693e5a7_720w.jpg)

另外，添加一个Spectrogram的组件，用于瀑布图的显示

![img](https://pic3.zhimg.com/80/v2-55376b55ac4d1b76603cc97feadd2bea_720w.jpg)

都设置好之后，然后还要具体设置显示的窗口，这里PothosFlow和GNURadio不同，后者在程序运行后，框图的组件会自动出来显示。但是在PothosFlow，我们要添加组件的显示前面板。在新增显示组件之前，你新建一个Graph page，可以把前面板的显示控件和代码分开来，类似于labview的编程类型了。

![img](https://pic1.zhimg.com/80/v2-41a6cdf6b52eec5230ad673c1a0f0f30_720w.jpg)

然年在PothosFlow中，在前面板对空白区域进行右键点击，选择弹出Insert graph widgets，

![img](https://pic2.zhimg.com/80/v2-6eb3436ffd697e487be895a867209a91_720w.jpg)

里面就可以把组件给添加出来了。如下图所示

![img](https://pic3.zhimg.com/80/v2-64abba25e9da840726f277e000e0ecea_720w.jpg)

最后，就直接根据下图的标红图标，点击之后即为运行状态，我们也可以在波形图和频谱图上看到对应的时率信息。这里需要注意的是，前面我们已经设置过中心频率为2.4GHz左右，才方便查看。

![img](https://pic4.zhimg.com/80/v2-3532bb64cbf3a5f3372a25ca0bce5203_720w.jpg)

以上该软件就可以运行了。我们另外说PothosFlow适合跑lora，这里给出一个simulation的示例。参考的代码为：[LoRa-SDR](https://link.zhihu.com/?target=https%3A//github.com/myriadrf/LoRa-SDR)。

下载完代码以后，在examples下面找到示例代码，然后手动把结尾改成PothosFlow可以读写的pth结果。改写完之后，用PothosFlow打开。

![img](https://pic4.zhimg.com/80/v2-59c7b336f015fff9f6b70c555ffdb69b_720w.jpg)

此时再点运行，我们就可以看到Lora可以运行仿真了。

![img](https://pic4.zhimg.com/80/v2-61a8bca1e7a5e1dc1c6f03e5cbfdf983_720w.jpg)

以上我们总结了PothosFlow的基本安装和使用方法，主要是笔者个人笔记用途，如果有不对的地方还请支出。

# CubicSDR和GQRX SDR（Windows平台）

## 序言

CubicSDR和GQRX SDR两个实际上都是偏应用软件，都属于接收机的软件了，作为应用的话，可以跑收音机，也可以做为采样波形用，比如说频谱检测，或者做回放时候采样数据用。这两个软件在安装PothosSDR也是一并被安装的。本文做一个简单介绍。

## CubicSDR

CubicSDR比较简单，安装完PothosSDR直接点击桌面图标就可以进入，它连接Pluto是通过SoapySDR连接的，这里我们不用手动设置，软件可以自己找到接口，如下图所示：

![img](https://pic4.zhimg.com/80/v2-18406c0bba252e701450146bcd9b743f_720w.jpg)

然后点击start就可以开始接收信号了

![img](https://pic3.zhimg.com/80/v2-92b11af5000f409e5498a1c256ffc116_720w.jpg)

左上角是调制模式，然后主屏幕是瀑布图，频谱图和时间轴的采样点。具体用法还是挺明显的，摸索一下就会用了。

## GQRX SDR

GQRX SDR相对于CubicSDR需要手动填写一下连接的设备名

![img](https://pic4.zhimg.com/80/v2-7116b0ac2a7d2c36deb04a2d8b7cccdb_720w.jpg)

这里有一些Device是可以默认选的，但是Pluto由于不是默认设备，需要自己填写Device string。设备类型建议选择为other。

然后Device string为

```text
device=plutosdr,driver=plutosdr,soapy=2,uri=usb:3.1.5
```

这里几项信息是通过

```text
SoapySDRUtil.exe --find
```

查看的，显示如下

![img](https://pic2.zhimg.com/80/v2-a712eebf620a8b05a935286caac2b509_720w.jpg)

device,driver直接名字对应，soapy=2这里指的是found device 2，最后一个是uri也是一一对应的。另外如果单独是USB的uri也可以用iio指令来看，如下图

![img](https://pic3.zhimg.com/80/v2-fad0fd95820192c5ae269593ee6baf56_720w.png)

填写好正确的Device string如下图

![img](https://pic2.zhimg.com/80/v2-77e7884eaf4719cb64345a4f5ac6be15_720w.jpg)

搞定后就可以直接运行了

![img](https://pic4.zhimg.com/80/v2-48674b3798b77c6c2db8dfb165c5cabb_720w.jpg)

这里使用方法也是很直观的，其实用法和CubicSDR是很类似的了。

# SDR-Sharp（Windows平台）

## 序言

SDR-Sharp也是一个比较实用的SDR软件，可以配合Pluto使用，本文做一个记录。

Ref：[https://www.rtl-sdr.com/plutosdr-quickstart-guide/](https://link.zhihu.com/?target=https%3A//www.rtl-sdr.com/plutosdr-quickstart-guide/)。

## SDR-Sharp

SDR-Sharp的原版可以在这里下载：[https://airspy.com/download/](https://link.zhihu.com/?target=https%3A//airspy.com/download/)

![img](https://pic4.zhimg.com/80/v2-2e803778dee379b653ffcfc137c96293_720w.jpg)

下载之后还需要另外下载一个Pluto的补丁，Pluto补丁：[sdrsharp-plutosdr](https://link.zhihu.com/?target=https%3A//github.com/Manawyrm/sdrsharp-plutosdr/releases)。

![img](https://pic3.zhimg.com/80/v2-bf8034b409169fcabbce8a670b6c3d3a_720w.png)

配置起来比较简单，需要把补丁里面的文件全部复制到SDRSharp里面替代原文件

然后需要编辑下**FrontEnds.xml**文件，如下图：

![img](https://pic2.zhimg.com/80/v2-1e1f3b8493c0cd9535471ec8bb0caac5_720w.jpg)

```text
<add key="PlutoSDR" value="SDRSharp.PlutoSDR.PlutoSDRIO,SDRSharp.PlutoSDR" />
```

需要将这一行加入到其配置文件里面，这样最后选source设备的时候才可以选Pluto。

我另外也整理了一个配置好的版本，这里：[sdrsharp-x86_pluto](https://link.zhihu.com/?target=https%3A//download.csdn.net/download/fzxy002763/12308549)。

最后运行就如下图了。其实SDRSharp套件里面还有好几个应用软件，不过主要是基于airspy的，直接跑Pluto还不行。

![img](https://pic4.zhimg.com/80/v2-ec0f9b906a326bd45d6950218b9ce6a3_720w.jpg)

其实SDR的几个应用软件从功能上而言差不多，主要是看哪个用起来上手习惯了。

