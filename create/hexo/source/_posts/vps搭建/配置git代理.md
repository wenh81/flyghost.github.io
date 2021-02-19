---
title: 配置git代理
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
description: 配置git代理
---

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

