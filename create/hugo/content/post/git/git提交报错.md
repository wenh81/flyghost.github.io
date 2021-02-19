+++
author = "creekwater"
title = "error: RPC 失败。curl 92 HTTP/2 stream 0 was not closed cleanly: CANCEL (err 8)"
date = "2021-02-01"
description = "error: RPC 失败。curl 92 HTTP/2 stream 0 was not closed cleanly: CANCEL (err 8)"
categories = [
    "GIT"
]
tags = [
    "GIT"
]

+++

# git 提交报错 error: RPC failed; curl 92 HTTP/2 stream 0 was not closed cleanly: PROTOCOL_ERROR (err 1)

```shell
error: RPC 失败。curl 92 HTTP/2 stream 0 was not closed cleanly: CANCEL (err 8)
send-pack: unexpected disconnect while reading sideband packet
```

# 原因

http2本身的bug

# 解决方法

#### 1、替换掉git的http的版本

```shell
git config --global http.version HTTP/1.1
```

#### 2、更改git的克隆方式http为ssh，使用ssh进行代码的下载和提交

