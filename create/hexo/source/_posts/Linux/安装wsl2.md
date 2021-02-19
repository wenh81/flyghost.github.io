---
title: 安装wsl2
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
description: 启用适用于 Linux 的 Windows 子系统
---



# 管理员权限打开终端，开启win10虚拟系统

```shell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

# 重启电脑

# 下载内核更新包并安装
https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

# 打开终端
```
wsl --set-default-version 2
```

# 在商店安装ubuntu20
