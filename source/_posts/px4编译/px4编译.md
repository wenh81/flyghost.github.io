---
title: px4编译
tags:
  - 无人机
originContent: ''
categories:
  - 无人机
toc: false
abbrlink: 7a082ea4
date: 2016-07-13 20:04:07
---

如果下载速度特别慢，可以使用手机的4G网络

# 位置确定

```
mkdir -p ~/src
cd ~/src
```

# 开始下载指定版本的px4，在这里是v1.8.2版本

```
git clone -b v1.8.2 https://github.com/PX4/Firmware.git 
```




# 添加子模块：

```
cd Firmware
git submodule init
git submodule update --init --recursive
```

# 显示版本号

```
git describe --always --tags
```

# 编译，缺什么安装什么

```
make px4fmu-v2_default

```
# 安装jinja

```
sudo apt-get install python-jinja2
```


# 需要安装的pip

```
sudo apt install python-pip 
sudo pip install numpy toml
```

# 若提示以下错误：确保cmake安装，重启系统即可

```
CMake Error: CMake was unable to find a build program corresponding to "Unix Makefiles".
```
# 提示flash溢出
1.注释掉不需要的模块（不怎么建议使用，后续会知道如何注释）；
2.修改默认的Flash大小。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190530150415678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIwMzE0MTMz,size_16,color_FFFFFF,t_70)
打开/Firmware/platforms/nuttx/nuttx-configs/px4fmu-v2/scripts/ld.script
修改如图所示1008为2032即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190530150537377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIwMzE0MTMz,size_16,color_FFFFFF,t_70)
