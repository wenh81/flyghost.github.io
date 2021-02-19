+++
author = "creekwater"
title = "配置apt代理"
date = "2021-01-25"
categories = [
    "科学上网"
]
tags = [
    "科学上网"
]

+++

# 方法一：临时

```shell
sudo apt-get -o Acquire::http::proxy="http://127.0.0.1:1080/" update
```

# 方法二：临时，配置终端走代理，使得apt命令走代理
```shell
export http_proxy=http://127.0.0.1:1080
```

- 详细的配置可以看另一篇终端配置

# 方法三：永久（不推荐）

```shell
sudo vim /etc/apt/apt.conf # 或者修改/etc/envrionment
# 增加如下命令
Acquire::http::proxy "http://127.0.0.1:1080/";
Acquire::ftp::proxy "ftp://127.0.0.1:1080/";
Acquire::https::proxy "https://127.0.0.1:1080/";
```

