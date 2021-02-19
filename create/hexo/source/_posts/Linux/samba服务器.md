---
title: Samba服务器
date: 2021-01-05 00:00:00

# 文章出处名称 #
from: 灵溪驿站

# 文章出处链接 #
url: www.creekwater.cn

# 文章作者名称 #
author: creekwater

# 文章作者签名 #
about: 当生活心怀歹毒地将一切都搞成了黑色幽默，我顺水推舟把自己变成了一个受过高等教育的流氓。

# 文章作者头像 #
avatar: https://www.notes.worstone.cn/image/hexo.png

# 是否开启评论 #
comments: true

# 文章标签 #
tags: linux

# 文章分类 #
categories: linux

# 文章摘要 #
description: ubuntu搭建samba服务器
---

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