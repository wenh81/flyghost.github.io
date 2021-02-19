---
title: RPC 失败。curl 92 HTTP/2 stream 0 was not closed cleanly
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
tags: GIT

# 文章分类 #
categories: GIT

# 文章摘要 #
description: RPC 失败。curl 92 HTTP/2 stream 0 was not closed cleanly
---

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

