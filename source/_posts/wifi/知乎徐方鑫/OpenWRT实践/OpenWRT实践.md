---
title: OpenWRT实践
swiper: false
swiperImg: /medias/11.jpg
top: false
tags: WLAN
categories: wifi6
abbrlink: eabb5bc4
date: 2021-03-15 00:00:00
---

###### OpenWRT实践
# 开发环境构建

## **序言**

无线路由器目前已经可以做很多的功能了，相应的扩展资源也越来越多。其中大部分都是直接基于OpenWRT平台直接进行开发的。笔者之前一直介绍的都是一些协议的理论知识，目前也更新一些笔者关于SDWN（Software Defined Wireless Networking）的实现中，所总结的一些实战类的笔记。笔者目前是采用WNDR3800作为硬件平台，性价比高。

**PS：由于路径问题，一般一个ubuntu系统只能够安装一个OpenWRT，否则会出现路径不匹配的问题。**

## **开发环境构建**

**第一步：**安装一些依赖包

```text
sudo apt-get install libncurses5-dev zlib1g-dev gawk flex patch git-core g++ subversion
```

**第二步：**下载OpenWRT源码

```text
git clone git://git.openwrt.org/14.07/openwrt.git
```

**第三步**：修改文件夹权限，为了编译方便，一般直接对源码文件夹赋予777的权限

```text
sudo chmod -R 777 openwrt
```

**第四步：**修改feeds源，用以安装所需的package以及luci界面

```text
gedit feeds.conf.default
```

打开文件后，注释掉一些不需要的源，一般使用前三个源即可

![img](https://pic2.zhimg.com/80/v2-89ae01e181d627c7d290ff3f078172a5_720w.png)

**第五步：**更新与安装feeds包

```text
./scripts/feeds update –a
./scripts/feeds install –a
```

**第六步：**固件的编译设置，我们要设置如下内容。注意，空格键是选择是否安装模块，如果是“*”的话，那么就是默认安装，如果是“M”的话，那么就是要手动加载。回车键是用来选择是否进入子栏的，在配置完固件之后，需要手动保存后再离开。

**1）**Target System类型为： Atheros AR7xx/AR9xx

![img](https://pic3.zhimg.com/80/v2-7e252606ff48580c301f34b959f73ea6_720w.png)

![img](https://pic4.zhimg.com/80/v2-23f4d51686b056d2dec66c717f06c487_720w.png)

**2）**Target Profile类型为：NETGEAR WNDR3700/WNDR3800/WNDRMAC，这里根据路由器型号进行选择，我们采用的路由器为WNDR3800。

![img](https://pic2.zhimg.com/80/v2-c84bfbc4cce951338fb18e2f646a7c59_720w.png)

![img](https://pic3.zhimg.com/80/v2-9d85a7db47c1ea50167ea7ba84a2ab5e_720w.png)

**3）**Target Images类型为：squashfs,其余的选项不选

![img](https://pic1.zhimg.com/80/v2-85e68ef2783bc5a40ad35de8960eca04_720w.png)

![img](https://pic3.zhimg.com/80/v2-83f1e8cdc077396736cfa75bf0927eb6_720w.png)

**4）**分别选中 <Advanced configuration options(for developers)>，<Build the OpenWrt SDK>，<Build the OpenWrt based Toolchain>

![img](https://pic1.zhimg.com/80/v2-7475af0899fab79258e75dbac1eda50c_720w.png)

**5）**选择<Luci—Collections--(*)luci>，如下图（选择时要注意是选择了M还是*，因为这两种不同的选择方式编译完的固件是有不同的，**这里还是强调全部用 \***，否则刷机完之后，还需要手动加载Luci界面）

![img](https://pic1.zhimg.com/80/v2-4d6aff914d0d3cb211c464700300bcdc_720w.png)

![img](https://pic4.zhimg.com/80/v2-574c5409f88b9eb69edde68cd8ddff67_720w.png)

![img](https://pic1.zhimg.com/80/v2-38c495dbad2b2f484ece0994931c4ca4_720w.png)

**6）**保存退出

![img](https://pic3.zhimg.com/80/v2-3d27190043ef51f29172d95ef7944f96_720w.png)

**7）**如果需要装OVS的话，那么需要手动取消bridge，即在make menuconfig后手动执行以下指令。

```text
echo '#CONFIG_KERNEL_BRIDGE is not set' >> .config
```

**注意：每次 make menuconfig以后都要执行这条指令。**


**第七步：**在openwrt源码文件中，添加编译时候需要附加的模块。在openwrt编译过程中，会从互联网上自行下载一些模块，不过由于网络以及数据源的问题，有部分数据包直接下载是存在问题的。故本文已经将该版本openwrt所需要的数据包进行整理，并整理如下：

[openwrt文件1](https://link.zhihu.com/?target=http%3A//download.csdn.net/detail/fzxy002763/9714024)

[openwrt文件2](https://link.zhihu.com/?target=http%3A//download.csdn.net/detail/fzxy002763/9714028)

上述文件解压缩以后，可以获得一个dl.tar.gz的文件，首先将其下载至本地，并传入开发环境中，然后用以下命令解压缩

```text
tar zvxf dl.tar.gz
```

解压缩之后，可以获得一个名为dl的文件夹。此时需要将该文件夹与openwrt目录下的内容进行合并，比如可以用以下指令（如果在图形界面里面，手动拖拽文件夹也行）

```text
cp ./dl/* openwrt/dl/
```

在openwrt编译过程中，如果dl目录中已经有下载好的模块，那么编译的时候就不会再行下载资源。

**第八步：**编译openwrt固件。直接在openwrt根目录下，执行以下指令即可

```text
make V=99
```

编译完的结果被保存在目录（openwrt/bin/ar71xx/）下，其中ar71xx路径名与固件配置时选择的芯片型号有关。以本文选择WNDR3800路由器为例，最后编译结果为

```text
openwrt-ar71xx-generic-wndr3800-squashfs-sysupgrade.bin
```

将该文件拷出后，我们可以进行最后一步的刷机操作。

# 路由器更新固件（U-boot）

## **序言**

在编译完一个OpenWRT的固件之后，本节我们叙述如何将该固件刷入路由器中，我们采用的路由平台为WNDR3800，并且路由器已经刷上了不死U-boot，从而我们可以通过图形界面进行刷机，更方便一些。

## **U-boot刷机**

**第一步**：重置WNDR3800的系统。

![img](https://pic4.zhimg.com/80/v2-5666ed8df91934468e145081d2453c97_720w.png)

如上图，在关机模式下，用笔或者钳子按住Reset按钮之后。再按电源开关进行开机，并且保持按住Reset不动，等待大约10~20s之后，释放Reset按钮。

**第二步**：用网线将电脑和路由器的LAN接口（如下图红色位置，Etherner（1~4）接口）连接，

![img](https://pic2.zhimg.com/80/v2-d463523ff003f89c91793f472ef8cc7d_720w.png)

路由默认的DHCP会给本地主机分配地址（故本地不要采用固定IP的设置），然后通过网页访问u-boot（即用网页直接访问[http://192.168.1.1](https://link.zhihu.com/?target=http%3A//192.168.1.1/)），如下图

![img](https://pic1.zhimg.com/80/v2-2d9de38d4325b9ba2714098ffa5c653c_720w.png)

**第二步**：刷新固件。选择u-boot控制台中的固件更新一栏。选择之前我们编译好的固件，闪存布局一栏保持自动识别，并勾选自动重启即可。

![img](https://pic4.zhimg.com/80/v2-8b8041114ce7c900851afe673b507473_720w.png)

确定更新即可，如下图

![img](https://pic4.zhimg.com/80/v2-76490a4683d9a28141747b5423ef3893_720w.png)

确定以后，等待上传完即可。

![img](https://pic2.zhimg.com/80/v2-082aaa536fbc3e8643b6da08bcb807d5_720w.png)

这里显示上传完之后，路由器会进行重启，这个过程可能需要1~3分钟，该过程中，保持等待即可，否则别的操作可能会一起u-boot的损坏。

**第三步**：重新登录路由器，即登录[http://1](https://link.zhihu.com/?target=http%3A//192.168.1.1/)[92.168.1.1](https://link.zhihu.com/?target=http%3A//192.168.1.1/)。如下

![img](https://pic1.zhimg.com/80/v2-260fc7e992b3b36411a4422db5622130_720w.png)

首先会让使用者设置一下账户和密码，这里使用者自行设定即可。设置后，整个刷机过程就完成了，进入openwrt后，就可以当做一般性的路由进行配置。

![img](https://pic2.zhimg.com/80/v2-a8af42d4e9ff62ae804a0ae8aa0a2dc9_720w.png)

# Click Modular Router

## **序言**

本文我们介绍一下，在OpenWRT平台上运行一个较为轻便有效的软路由系统Click Modular Router，基于该软路由，我们可以扩展一些OpenWRT原有的功能。

## **Click Modular Router**

**第一步：**获取Click源码，源码可以从官方网站下载（[Click官网](https://link.zhihu.com/?target=http%3A//www.read.cs.ucla.edu/click/click)），也可以下载我们的整理的版本（[Click源码包以及Makefile](https://link.zhihu.com/?target=http%3A//download.csdn.net/detail/fzxy002763/9714663)）。其中包含两个文件，一个是源码（click-2.0.1.tar.gz），一个是Makefile，之后我们需要对该Makefile进行手动添加。

**第二步**：在openwrt源码目录下，建立click文件夹，并将源码剪贴到这个文件夹内。具体添加的目录如下：

```text
/home/openwrt/Desktop/openwrt/package/feeds/packages/click
```

即在（/home/openwrt/Desktop/openwrt/package/feeds/packages）这个目录下，新建click文件夹，然后将click-2.0.1.tar.gz解压之后的源码（即文件夹内的内容），全数复制到该文件夹内。解压click-2.0.1.tar.gz的指令如下：

```text
tar -zvxf ./click-2.0.1.tar.gz
```

**第三步**：修改下载的Makefile，并同样复制到前面第二步新建的目录下。其中需要修改的Makefile部分如下：

```text
#
# Copyright (C) 2006-2012 OpenWrt.org
#
# click Makefile by cqupt
# 
#

include $(TOPDIR)/rules.mk

PKG_NAME:=click
PKG_VERSION:=2.0.1
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=http://www.read.cs.ucla.edu/click/

include $(INCLUDE_DIR)/package.mk

define Package/click
        SECTION:=net
        CATEGORY:=Network
        TITLE:=The Click Modular Router
        MAINTAINER:=Roberto Riggio <roberto.riggio@gmail.com>
        DEPENDS:=+kmod-tun +libpcap +libstdcpp
        URL:=http://www.read.cs.ucla.edu/
endef

define Package/click/Description
        The Click Modular Router
endef


CONFIGURE_ARGS += \
  --enable-local\
        --enable-tools=host \
        --enable-userlevel \
        --host=mips-linux \
        --build=mips \
        --enable-wifi \
        --disable-linuxmodule \
        --disable-dynamic-linking \

define Build/Compile
        $(call Build/Install/Default, install)
endef


define Package/click/install
        $(INSTALL_DIR) $(1)/usr/bin
        $(CP) ~/Desktop/openwrt/build_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/uClibc-0.9.33.2/lib/librt.so.0 $(1)/usr/bin
        $(CP) ~/Desktop/openwrt/build_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/uClibc-0.9.33.2/lib/libpthread.so.0 $(1)/usr/bin
        $(INSTALL_BIN) $(PKG_BUILD_DIR)/userlevel/click $(1)/usr/bin/click
        $(INSTALL_BIN) $(PKG_BUILD_DIR)/tools/click-align/click-align $(1)/usr/bin/click-align
        mkdir -p $(1)/usr/share/click
        $(CP) $(PKG_BUILD_DIR)/elementmap.xml $(1)/usr/share/click 
endef

$(eval $(call BuildPackage,click))
```

其中第48行和第49行需要修改为用户对应的目录下，即如下黑体的部分即是要修改的路径。

- $(CP) **~/Desktop/openwrt/**build_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/uClibc-0.9.33.2/lib/librt.so.0 $(1)/usr/bin
- $(CP) **~/Desktop/openwrt/**build_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/uClibc-0.9.33.2/lib/libpthread.so.0 $(1)/usr/bin

如果不做修改的话，在编译的时候，有可能会报如下的错误：

```text
cp: cannot stat '/home/your_dir/openwrt/build_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/uClibc-0.9.33.2/lib/librt.so.0': No such file or directory
```

修改完之后，复制到之前的目录下：

```text
/home/openwrt/Desktop/openwrt/package/feeds/packages/click
```

**第四步**：在openwrt根目录下，直接编译固件模块

```text
make package/feeds/packages/click/compile V=99
```

编译后，可以直接把编译完的ipk安装包导入路由，并且进行安装，也在menuconfig中添加click，再将整个固件重新编译。

**第五步**：进行固件配置（添加选择 <Network - click>模块），如下图

![img](https://pic2.zhimg.com/80/v2-1d73c111a5eb4384667783cb5cfd9045_720w.png)

![img](https://pic2.zhimg.com/80/v2-d2bfe441656396b239d4f75d442d0eb1_720w.png)

编译完之后注意保存，然后退出。最后添加以下命令，用以取消bridge：

```text
echo '# CONFIG_KERNEL_BRIDGE is not set' >> .config
```

**第六步**：重新编译固件，最后再更新路由器即可。

```text
make V=99
```

# Open vSwitch

## **序言**

本文我们介绍OVS在OpenWRT下的编译，编译OVS一般都是为SDN的应用所预设的。与click不同的是，OVS的添加可以不用手动编译，而是直接通过feeds的方式进行添加即可，只是具体配置的过程会复杂一些。

注：一些配置过程也可以参考：[github](https://link.zhihu.com/?target=https%3A//github.com/fzxy002763/openvswitch)。

## **Open vSwitch编译**

**第一步：**安装依赖，这里有可能会存在之前编译openWRT以外还额外需要的程序包，所以需要添加一下。如果不添加，直接利用以前的环境可能问题也不大，笔者建议还是添加一下为好。

```text
sudo apt-get install gcc g++ binutils patch bzip2 flex bison make autoconf gettext texinfo unzip sharutils subversion libncurses5-dev ncurses-term zlib1g-dev subversion git gawk asciidoc libz-dev
```

**第二步：**在feeds的配置文件中添加OVS的源，即采用以下命令：

```text
echo 'src-git openvswitch git://github.com/pichuang/openvwrt.git' >> feeds.conf
```

该命令中描述将该行内容添加进feeds.conf文件中，feeds软件可以根据这个文件，进行OVS的下载。我们解释该行命令如下

- 在该命令中，src-git代表源的类型，这里的源是git类型的，除了src-git以外，还包含以下几种类型：

- - **src-hg**：通过使用hg从数据源path/URL下载数据
  - **src-cpy**：从数据源path拷贝数据
  - **src-svn**：通过使用svn从数据源path/URL下载数据
  - **src-bzr**：通过使用bzr从数据源的pxiaath/URL下载数据
  - **src-link**：创建一个数据源path的symlink
  - **src-darcs**：通过使用darcs从数据源path/URL下载数据

- 该命令中，openvswitch是代表将这个源命名为openvswitch，后文可以指定用feeds安装该源，如下就是指定更新和安装openvswitch组件：

- - ./scripts/feeds update openvswitch
    ./scripts/feeds install -a -p openvswitch

- 该命令中，最后的（git://[http://github.com/pichuang/openvwrt.git](https://link.zhihu.com/?target=http%3A//github.com/pichuang/openvwrt.git)）代表真实源的地址。

**第三步：**更新和安装OVS的源，以及安装一个patch

```text
./scripts/feeds update openvswitch 
./scripts/feeds install -a -p openvswitch
```

安装patch（为了避免源失效，我们这里对patch做了一个备份：[OVS的patch文件 - 下载频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//download.csdn.net/detail/fzxy002763/9716599)）

```text
wget https://gist.githubusercontent.com/pichuang/7372af6d5d3bd1db5a88/raw/4e2290e3e184288de7623c02f63fb57c536e035a/openwrt-add-libatomic.patch -q -O - | patch -p1
```

**第四步：**编译组件，请按照以下步骤一步步进行操作。

\1. 运行make menuconfig，勾选（Advanced configuration options (for developers) -> Toolchain Options 和 Advanced configuration options (for developers) -> Target Options），然后保存退出。

![img](https://pic4.zhimg.com/80/v2-a5fdb48f6cd4acee9e1dd2c79ce4673b_720w.png)

![img](https://pic3.zhimg.com/80/v2-b26e6c0eff35d3d08db73d773ec56a16_720w.png)

\2. 勾选（Network -> openvswitch-switch, openvswitch-switch, openvswitch-ipsec (Optional)）

![img](https://pic3.zhimg.com/80/v2-d4d44e423ba47ffa403135704285a03e_720w.png)

![img](https://pic1.zhimg.com/80/v2-b09fbbc5310daabc77945d66940318a4_720w.png)

\3. 勾选（Advanced configuration options (for developers) -> Toolchain Options -> Binutils Version -> Linaro binutils 2.24（SELECT）），取消勾选（Advanced configuration options (for developers) -> Target Options -> Build packages with MIPS16 instructions（UNSELECT）），然后保存退出。

![img](https://pic2.zhimg.com/80/v2-0d7260fbfab76878e637ef288b8db1a5_720w.png)

![img](https://pic3.zhimg.com/80/v2-51b6ad82c8018963df2588e41ac546aa_720w.png)

![img](https://pic2.zhimg.com/80/v2-a7955a464456aefb03f6bce46a5dd209_720w.png)

![img](https://pic4.zhimg.com/80/v2-ca9b999fb67625da1d09465cea11e27b_720w.png)

![img](https://pic1.zhimg.com/80/v2-1d9e8e404800a7d9825be4109e0ad23c_720w.png)

![img](https://pic4.zhimg.com/80/v2-d2fee7aa3a5e5fbdea0087ae6c887c33_720w.png)

**注意**：这里需要注意先做步骤1（第四步中的步骤1），之后再做该步骤，否则会发现（Advanced configuration options (for developers) -> Toolchain Options 和 Advanced configuration options (for developers) -> Target Options）两个文件夹内都是空的，如下图：

![img](https://pic2.zhimg.com/80/v2-f4b7218afc8279ac8d116e7ee1bd67cd_720w.png)

![img](https://pic2.zhimg.com/80/v2-2ab03f528205a6e4c4e894fd3d694f35_720w.png)

**第五步：**每一次menuconfig之后，都需要执行运行命令，即取消Bridge。

```text
echo '# CONFIG_KERNEL_BRIDGE is not set' >> .config
```

**第六步：**编译固件，这里建议采用V=s的配置，代替V=99的配置，笔者测试中，后者可能会有点问题，这个编译时间比较长，慢慢等待即可。

```text
make V=s
```

**第七步：**烧写固件到路由器，这里参阅之前的文档中的操作即可。

**注意**：烧写固件之后，需要修改/etc/config/wireless文件，将wireless disable 1中的1改为0，然后进行reboot操作，用以开启无线。

# Feeds安装本地源

## **序言**

本文是笔者自己的一个笔记，当时在前面《[OpenWRT实践3：Click Modular Router](https://zhuanlan.zhihu.com/p/24431825)》这个做的时候，是将整个Click包都放到了feeds对应的目录下，最后编译也是整个编译过去的，但是实际情况下，这样操作比较复杂，因为整个过程相当于首先先把所有的包全部下载好，然后把自定义的部分也都添加进去，最后再整理编译。能不能直接利用feeds的功能直接安装自定义的本地包呢。本文仅仅针对于这个功能做一个笔记。

## Feeds安装本地包

一个默认的 feed.conf 如下：

```text
src-git packages https://github.com/openwrt/packages.git;for-14.07
src-git luci https://github.com/openwrt/luci.git;luci-0.12
src-git routing https://github.com/openwrt-routing/packages.git;for-14.07
src-git telephony https://github.com/openwrt/telephony.git;for-14.07
src-git management https://github.com/openwrt-management/packages.git;for-14.07
src-git oldpackages http://git.openwrt.org/14.07/packages.git
#src-svn xwrt http://x-wrt.googlecode.com/svn/trunk/package
#src-svn phone svn://svn.openwrt.org/openwrt/feeds/phone
#src-svn efl svn://svn.openwrt.org/openwrt/feeds/efl
#src-svn xorg svn://svn.openwrt.org/openwrt/feeds/xorg
#src-svn desktop svn://svn.openwrt.org/openwrt/feeds/desktop
#src-svn xfce svn://svn.openwrt.org/openwrt/feeds/xfce
#src-svn lxde svn://svn.openwrt.org/openwrt/feeds/lxde
#src-link custom /usr/src/openwrt/custom-feed
```

我们可以看到，里面添加的源可以是以src-git添加的github上面的源，也可以是src-svn形式添加的svn源。

其实还有一种就是可以添加本地源，如这种写法：

```text
src-link custom /home/openwrt/Desktop/odin/custom
```

这是以src-link来写的，这样写的好处是可以将自定义的一些源文件直接以feed的形式添加进来，比如说之前我们两篇文章说过的click和openvswitch。

笔者对应的custom内容如下：

![img](https://pic2.zhimg.com/80/v2-3aaf7b8b8a11b43b72fd1bf3e0e358bd_720w.png)

实际上就是修改过的click和openvswith两个组件，修改的内容和前面两篇文章相同。

然后就可以直接以feed的形式更新了。

第一步：把本地源添加上去，可以直接代码添加

```text
echo "src-link custom `pwd`/custom" >> openwrt/feeds.conf
```

第二步：用feed直接安装上去

```text
./scripts/feeds update custom
./scripts/feeds install -p custom click
./scripts/feeds install -p custom openvswitch-common
```

这样子修改过的Click和openvswitch就直接可以添加上去了。

最后编辑镜像的时候，在make menuconfig里面选上click和openvswitch就可以了，这个部分也可以用代码直接写，如下：

```text
sed -i.orig \
     -e 's/# \(CONFIG_PACKAGE_click\) is not set/\1=y/' \
     -e 's/# \(CONFIG_PACKAGE_openvswitch-common\) is not set/\1=y/' \
     -e 's/# \(CONFIG_PACKAGE_openvswitch-ipsec\) is not set/\1=y/' \
     -e 's/# \(CONFIG_PACKAGE_openvswitch-switch\) is not set/\1=y/' \
   .config
```

以上就是利用feeds机制安装本地源的过程，笔者这里主要做一个笔记给自己用的，有问题的地方还请见谅。

