---
title: python版本设置
date: 2021-01-05 00:00:00
swiper: true
swiperImg: '/medias/2.jpg'
top: true
tags: Linux
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

#### 方法一（不建议）：alias

#### 方法二（不建议）：关闭sip来设置

在新版macos系统下，系统默认设置需要关闭sip来进行修改。

#### 方法三（推荐）：软连接

在```/usr/local/bin```下新建一个python的软连接，由于PATH中该路径是优先的，在寻找python时优先在该路径下寻找，所以这个路径下有python软连接会覆盖```/usr/bin```中的python软连接，实现修改默认python

- 确保PATH中包含```/usr/local/bin``` ，使用```cat $PATH```查看，不是的话设置```export PATH=/usr/local/bin:$PATH```

- 添加软连接 ```ln -s /usr/local/bin/python3 /usr/local/bin/python```

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
