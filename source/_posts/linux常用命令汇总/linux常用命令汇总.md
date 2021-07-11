---
title: linux常用命令汇总
swiper: true
tags: LINUX
categories: LINUX
abbrlink: 337fc13d
date: 2021-01-05 00:00:00
---



# Ubuntu20设置python2为默认python
```shell
sudo rm /usr/bin/python 
sudo ln -s /usr/bin/python2 /usr/bin/python 
```

# Ubuntu20设置python3为默认python

```shell
sudo apt install python-is-python3
```

# macOS 下设置python3为默认python

## 方法一（不建议）：alias

## 方法二（不建议）：关闭sip来设置

在新版macos系统下，系统默认设置需要关闭sip来进行修改。

## 方法三（推荐）：软连接

在```/usr/local/bin```下新建一个python的软连接，由于PATH中该路径是优先的，在寻找python时优先在该路径下寻找，所以这个路径下有python软连接会覆盖```/usr/bin```中的python软连接，实现修改默认python

- 确保PATH中包含```/usr/local/bin``` ，使用```cat $PATH```查看，不是的话设置```export PATH=/usr/local/bin:$PATH```
- 添加软连接 ```ln -s /usr/local/bin/python3 /usr/local/bin/python```

如果在M1平台，可以将Python3连接到homebrew平台下

- ``ln -s /opt/homebrew/bin/python3 /opt/homebrew/bin/python``



# Ubuntu更换阿里源

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo sed -i 's/security.ubuntu/mirrors.aliyun/g' /etc/apt/sources.list
sudo sed -i 's/archive.ubuntu/mirrors.aliyun/g' /etc/apt/sources.list
sudo apt update
sudo apt-get upgrade	# 更新已安装的包到最新，这个是可选的
```

# Linux安装samba服务

```shell
sudo apt-get install samba 

# Linux创建共享文件
# 新建文件夹，并右击属性设置为 sambashare 或者 sudo chmod 777 /home/zsh/share
# Samba配置
sudo vim /etc/samba/smb.conf 


# 加入：[share]
# 设置共享目录

path = /home/wzy/share

# 设置访问用户 
valid users = wzy

# 设置读写权限
writable = yes  

# Linux 设置samba共享用户密码
sudo useradd wzy # 新建用户，可以直接使用已有用户
sudo smbpasswd -a wzy
sudo service smbd restart

# linxu访问
smb://192.168.0.216/

# Windows访问,输入用户名和密码，即可访问
\\192.168.0.216\

# 查看samba服务器中已拥有哪些用户：
sudo pdbedit -L

# 删除samba服务中的某个用户
sudo smbpasswd -x 用户名

# 删除linux某个用户
sudo userdel 用户名

# 删除linux中某个用户所有信息
sudo userdel -r 用户名
```

# vscode无法远程连接wsl

```shell
# 重启wsl
wsl.exe --shutdown
wsl.exe
```

# 安装wsl

```shell
# 管理员权限打开终端，开启win10虚拟系统
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# 重启电脑
# 下载内核更新包并安装
https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

# 设置wsl版本
wsl --set-default-version 2

# 在商店安装ubuntu20
```

# linux 交叉编译链

```
# 安装arm-linux-gcc
sudo apt-get install g++-arm-linux-gnueabihf

# 安装arm-linux-g++
sudo apt-get install g++-arm-linux-gnueabihf
```

```shell
# 卸载arm-linux-gcc
sudo apt-get remove gcc-arm-linux-gnueabihf

# 卸载arm-linux-g++
sudo apt-get remove g++-arm-linux-gnueabihf
```

# mac访问linux文件

```shell
# ubuntu开启FTP服务
sudo apt-get install vsftpd

sudo vim /etc/vsftpd.conf
```

打开 vsftpd.conf 文件以后找到如下两行

```shell
local_enable=YES
write_enable=YES
```

确保上面两行前面没有“#”，有的话就取消掉

```shell
sudo /etc/init.d/vsftpd restart
```

mac打开浏览器，输入：

```shell
# 下面的IP是Linux的IP地址
ftp://192.168.0.112/
```

然后会自动打开finder，填入linux的用户名和密码即可

# mac安装CH340驱动

```shell
http://www.wch.cn/download/CH341SER_MAC_ZIP.html
```

