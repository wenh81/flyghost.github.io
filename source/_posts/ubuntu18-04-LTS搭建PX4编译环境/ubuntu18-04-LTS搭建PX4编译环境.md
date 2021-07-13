---
title: ubuntu18.04-LTS搭建PX4编译环境
tags:
  - 无人机
originContent: ''
categories:
  - 无人机
toc: false
abbrlink: 1e83aa5d
date: 2016-07-13 20:05:27
---

这里先给出官方的教程[px4环境搭建](http://dev.px4.io/zh/setup/dev_env_linux.html)
由于个人按照官方的教程总是有各种问题，故总结几个必要的安装命令，环境也成功搭建
# 安装python、git、qt
```
sudo apt-get install python-argparse git-core wget zip \
    python-empy qtcreator cmake build-essential genromfs -y
```
# 安装仿真软件和需要的库文件，注意体验下面两句的区别（一句代码后有 -y ，一句没有）
```
 sudo apt-get install openjdk-8-jre
 sudo apt-get install ant protobuf-compiler libeigen3-dev libopencv-dev openjdk-8-jdk openjdk-8-jre clang-3.5 lldb-3.5 -y
```
# 卸载modemmanager模式管理器
Ubuntu配备了一系列代理管理，这会严重干扰相关的串口（或usb串口），最明显的表现就是硬件连接到PC机后，无法读出硬件，无法烧录上传固件
```
sudo apt-get remove modemmanager
```
# 更新、安装相关的库
```
sudo apt-get install python-serial openocd \
    flex bison libncurses5-dev autoconf texinfo build-essential \
    libftdi-dev libtool zlib1g-dev \
    python-empy gcc-arm-none-eabi -y
```

# 安装正确版本的gcc-arm-none-eabi
还记的上面出现的那个Failed吗？实际我们安装的gcc-arm-none-eabi版本不支持源码，所以需要安装正确的版本。输入一下代码可以查看gcc-arm-none-eabi版本信息

```
arm-none-eabi-gcc --version
```

# 下载GCC4.9.4或5.4.3

```
直接使用该命令下载也可以
sudo apt-get install gcc-arm-none-eabi
```

GCC4.9.4
```
wget https://launchpad.net/gcc-arm-embedded/4.9/4.9-2015-q3-update/+download/gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar.bz2
```
GCC5.4.3
```
wget https://launchpad.net/gcc-arm-embedded/5.0/5-2016-q2-update/+download/gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
```

# 安装GCC4.9.4
在压缩包同一路径下，新建一个xxx.sh文件，将下述代码法制到脚本文件中
```
pushd .
# 卸载新版的gcc-arm-none-eabi
sudo apt-get remove gcc-arm-none-eabi
# 安装下载好的gcc-arm-none-eabi
# 解压
tar -jxf gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar.bz2
# 移动
sudo mv gcc-arm-none-eabi-4_9-2015q3 /opt
exportline="export PATH=/opt/gcc-arm-none-eabi-4_9-2015q3/bin:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
# 使路径生效
. ~/.profile
popd
```

# 或者安装GCC5.4.3
在压缩包同一路径下，新建一个xxx.sh文件，将下述代码法制到脚本文件中
```
pushd .
# 卸载新版的gcc-arm-none-eabi
sudo apt-get remove gcc-arm-none-eabi
# 安装下载好的gcc-arm-none-eabi
# 解压
tar -jxf gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
# 移动
sudo mv gcc-arm-none-eabi-5_4-2016q2 /opt
exportline="export PATH=/opt/gcc-arm-none-eabi-5_4-2016q2/bin:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
# 使路径生效
. ~/.profile
popd
```

修改脚本执行权限
```
chmod +x xxx.sh
```
运行该脚本
```
./xxx.sh
```
# 编译源码
首先需要安装一个库文件
```
sudo apt-get install lsb-core
```
编译过程中需要安装其他库，在编译一文中有介绍
代码下载编译请见
**[px4编译](https://blog.csdn.net/qq_20314133/article/details/90374584)**