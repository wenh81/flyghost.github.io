---
title: v2ray科学上网
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
tags: 科学上网

# 文章分类 #
categories: 科学上网

# 文章摘要 #
description: 使用v2ray协议进行科学上网
---



# 1 安装和更新 V2Ray

```
// 安装可执行文件和 .dat 数据文件
# bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

# 2 安装最新发行的 geoip.dat 和 geosite.dat

```shell
// 只更新 .dat 数据文件
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh)
```

# 3 移除 V2Ray

```shell
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh) --remove
```

# 4 修改配置文件

```shell
vim /usr/local/etc/v2ray/config.json
systemctl enable v2ray
systemctl start v2ray
```

# 5 配置为websocket

## 5.1 server端

```json
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 10809,
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": "b22e94d6-569d-6d71-46fd-f2183d92375c"
                    }
                ]
            },
            "streamSettings": {
                "network": "ws"
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}

```

## 5.2 client端

![vmess_ws.png](vmess_ws.png)

# 6 配置为TCP

## 6.1 server

```json
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 10809,
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": "b22e94d6-569d-6d71-46fd-f2183d92375c"
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp"
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}
```

## 6.2 client

![vmess_ws.png](vmess_tcp.png)

# 快速链接

```
vmess://ew0KICAidiI6ICIyIiwNCiAgInBzIjogImNyZWVrd2F0ZXIiLA0KICAiYWRkIjogImNyZWVrd2F0ZXIuaWN1IiwNCiAgInBvcnQiOiAiMTA4MDkiLA0KICAiaWQiOiAiYjIyZTk0ZDYtNTY5ZC02ZDcxLTQ2ZmQtZjIxODNkOTIzNzVjIiwNCiAgImFpZCI6ICIwIiwNCiAgIm5ldCI6ICJ3cyIsDQogICJ0eXBlIjogIm5vbmUiLA0KICAiaG9zdCI6ICIiLA0KICAicGF0aCI6ICIiLA0KICAidGxzIjogIiINCn0=
```
