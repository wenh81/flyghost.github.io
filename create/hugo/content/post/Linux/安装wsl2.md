+++
author = "creekwater"
title = "安装wsl2"
date = "2021-01-06"
description = "启用适用于 Linux 的 Windows 子系统"
categories = [
    "linux"
]
tags = [
    "linux"
]

+++

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
