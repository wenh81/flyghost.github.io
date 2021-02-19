+++
author = "creekwater"
title = "git仓库迁移并保存提交历史"
date = "2021-02-01"
description = "git仓库迁移并保存提交历史"
categories = [
    "GIT"
]
tags = [
    "GIT"
]

+++

#### 

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

