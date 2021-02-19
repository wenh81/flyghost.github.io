---
title: ubuntu更换源
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
description: python版本设置
---

# Ubuntu更换阿里源
```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo sed -i 's/security.ubuntu/mirrors.aliyun/g' /etc/apt/sources.list
sudo sed -i 's/archive.ubuntu/mirrors.aliyun/g' /etc/apt/sources.list
sudo apt update
sudo apt-get upgrade	# 更新已安装的包到最新，这个是可选的
```


