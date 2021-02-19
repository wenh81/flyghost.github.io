+++
author = "creekwater"
title = "Samba服务器"
date = "2021-01-06"
description = "ubuntu搭建samba服务器"
categories = [
    "linux"
]
tags = [
    "linux"
]

+++

# Linux安装samba服务
```shell
sudo apt-get install samba 
```

# Linux创建共享文件

- 新建文件夹，并右击属性设置为 sambashare 或者 sudo chmod 777 /home/zsh/share
- Samba配置
  ```shell
sudo vim /etc/samba/smb.conf 
  ```
- 加入：[share]
# 设置共享目录
```shell
path = /home/wzy/share
```

# 设置访问用户 
```shell
valid users = wzy
```

# 设置读写权限
```shell
writable = yes  
```

Linux 设置samba共享用户密码
```shell
sudo useradd wzy # 新建用户，可以直接使用已有用户
sudo smbpasswd -a wzy
sudo service smbd restart
```

# linxu访问
```shell
smb://192.168.0.216/
```
# Windows访问,输入用户名和密码，即可访问
```shell
\\192.168.0.216\
```

# 查看samba服务器中已拥有哪些用户：
```shell
sudo pdbedit -L
```

# 删除samba服务中的某个用户
```shell
sudo smbpasswd -x 用户名
```
# 删除linux某个用户
```shell
sudo userdel 用户名
```
# 删除linux中某个用户所有信息
```shell
sudo userdel -r 用户名
```