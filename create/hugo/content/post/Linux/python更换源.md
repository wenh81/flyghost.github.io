+++
author = "creekwater"
title = "ubuntu更换源"
date = "2020-01-06"
categories = [
    "linux"
]
tags = [
    "linux"
]

+++


# Ubuntu更换阿里源
```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo sed -i 's/security.ubuntu/mirrors.aliyun/g' /etc/apt/sources.list
sudo sed -i 's/archive.ubuntu/mirrors.aliyun/g' /etc/apt/sources.list
sudo apt update
sudo apt-get upgrade	# 更新已安装的包到最新，这个是可选的
```


