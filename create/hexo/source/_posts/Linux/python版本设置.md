---
title: python版本设置
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



# Ubuntu20设置python2为默认python
```shell
sudo rm /usr/bin/python 
sudo ln -s /usr/bin/python2 /usr/bin/python 
```

# Ubuntu20设置python3为默认python

```shell
sudo apt install python-is-python3
```

# macOS 下设置python3为默认python

#### 方法一（不建议）：alias

#### 方法二（不建议）：关闭sip来设置

在新版macos系统下，系统默认设置需要关闭sip来进行修改。

#### 方法三（推荐）：在```/usr/local/bin```下新建一个python的软连接，由于PATH中该路径是优先的，在寻找python时优先在该路径下寻找，所以这个路径下有python软连接会覆盖```/usr/bin```中的python软连接，实现修改默认python

- 确保PATH中包含```/usr/local/bin``` ，使用```cat $PATH```查看，不是的话设置```export PATH=/usr/local/bin:$PATH```

- 添加软连接 ```ln -s /usr/local/bin/python3 /usr/local/bin/python```

