---
title: 配置apt代理
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
description: 配置apt代理
---

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

