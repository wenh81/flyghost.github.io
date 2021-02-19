+++
author = "creekwater"
title = "配置git代理"
date = "2021-01-25"
categories = [
    "科学上网"
]
tags = [
    "科学上网"
]

+++

# 方法一：使用socks5协议，10808端口修改成自己的本地代理端口

```shell
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
```

# 方法二：使用http协议，10809端口修改成自己的本地代理端口

```shell
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```

# 查看所有配置
```shell
git config -l
```

# reset 代理设置

```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

