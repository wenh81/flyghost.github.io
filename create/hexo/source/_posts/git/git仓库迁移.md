---
title: git仓库迁移并保存提交历史
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
description: git仓库迁移并保存提交历史
---

# 删除原有的git地址

```shell
git remote rm origin
```

# 添加新地址

```shell
git remote add origin new_rep.git
```

# 提交代码

```shell
git push -u origin master
```

