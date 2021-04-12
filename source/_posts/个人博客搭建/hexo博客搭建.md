---
title: hexo博客搭建
swiper: false
swiperImg: /medias/11.jpg
top: false
tags: 博客
abbrlink: afcbc60d
date: 2021-02-23 00:00:00
---

# 安装 Node.js和npm

> sudo apt-get update
> sudo apt-get install nodejs
> sudo apt-get install npm

如果报错,请更改软件源--[清华大学开源软件源](https://link.jianshu.com?t=https%3A%2F%2Fmirrors.tuna.tsinghua.edu.cn%2Fhelp%2Fubuntu%2F),并更新

查看nodejs和npm版本号

> nodejs -v
> npm -v

可以正常打印版本号说明,安装成功

# 安装 Hexo

创建博客所在目录

> mkdir hexo

安装 Hexo

> 创建目录
> mkdir hexo
> 切换目录
> cd hexo
> 全局安装 Hexo，需要最高权限，记得输入root密码
> sudo npm install -g hexo-cli
> 初始化 Hexo
> hexo init

如果报错执行代码,不报错忽略

> sudo npm config set user 0
> sudo npm config set unsafe-perm true
> sudo npm install -g hexo-cli

安装插件

> npm install hexo-generator-index --save
> npm install hexo-generator-archive --save
> npm install hexo-generator-category --save
> npm install hexo-generator-tag --save
> npm install hexo-server --save
> npm install hexo-deployer-git --save
> npm install hexo-deployer-heroku --save
> npm install hexo-deployer-rsync --save
> npm install hexo-deployer-openshift --save
> npm install hexo-renderer-marked --save
> npm install hexo-renderer-stylus --save
> npm install hexo-generator-feed --save
> npm install hexo-generator-sitemap --save

测试安装成功

> hexo server

![img](https:////upload-images.jianshu.io/upload_images/2598556-5265065995c5159c?imageMogr2/auto-orient/strip|imageView2/2/w/406/format/webp)

成功的提示

浏览器输入 http://localhost:4000 可以访问到首页



![img](https:////upload-images.jianshu.io/upload_images/2598556-4f669806bd1340e6?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

这里写图片描述

修改Hexo配置文件hexo/_config.yml

> 提示：key对应没有值的时候，冒号后面一定要有空格！否则会报错
> 例如: timezone:会报错，timezone: 则不会。
> 下边列出了常用的配置信息

站点信息设置

> title: ZTFDeveloper's Blog #站名
> subtitle:  #副标题
> description: 前进的开发者#站描述
> author:  ZTF#作者
> language: zh-CN #语言
> timezone:

想把博客部署到自己的服务器,并通过IP或域名访问需要配置url

> url: [http://blog.prozin.xyz](https://link.jianshu.com?t=http%3A%2F%2Fblog.prozin.xyz)
> root: /
> permalink: :year/:month/:day/:title/
> permalink_defaults:

Deployment 这里设置了Git,mocorochio为你的github用户名

> deploy:
> type: git
> repo: [git@github.com](https://link.jianshu.com?t=mailto%3Agit%40github.com):mocorochio/[micorochio.github.io.git](https://link.jianshu.com?t=http%3A%2F%2Fmicorochio.github.io.git)
> branch: master

# 三、Hexo 相关命令

新建文章(会在 hexo/source/_post 下生成对应.md 文件)

> hexo n “文章名称”

生成静态文件(位于 hexo/public 目录)

> hexo g

启动 Hexo 预览

> hexo s

提交部署(需要相关配置)

> hexo d



# 安装nginx

省略。。。

# 部署网站

### 下载静态网站

```shell
mkdir /www
cd /www
git clone -b public https://github.com/flyghost/flyghost.github.io.git
```

将**flyghost**改为你自己的用户名，由于我将静态网站发布到**public**分支，所以需要加上**```-b public```**

### 申请SSL证书

阿里云申请，省略。。。

### 下载SSL证书

将SSL证书放入**```/ssl/www.creekwater.cn/```**路径下

### 配置nginx

```shell
# 如果刚安装的nginx，该路径下为空
cd /etc/nginx/conf.d

# 新建一个配置文件
vim www.creekwater.cn.conf
```

### 编辑配置文件

```javascript
server {
    listen 80;                                              # http端口
    server_name  www.creekwater.cn;                        # 改为自己的域名
    rewrite ^(.*)$ https://${server_name}$1 permanent;      # http重定向到https，可以实现所有访问都转到https
}

server {
    server_name www.creekwater.cn;     # 改为域名
    listen 443 ssl;                     # 一定要改为443，因为https端口为443，同时开启SSL

    ssl_certificate /ssl/www.creekwater.cn/www.creekwater.cn.pem;         # 改为自己申请得到的 crt 文件的名称
    ssl_certificate_key /ssl/www.creekwater.cn/www.creekwater.cn.key;     # 改为自己申请得到的 key 文件的名称
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;

    root /www/flyghost.github.io;                                           # 静态网站路径，注意权限
    index index.html;                                                       # 主页名字
    location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|pdf|txt){
        root /www/flyghost.github.io;                                       # 静态网站路径
    }
}
```

### 重启nginx

```shell
nginx -s reload
```

# 常见问题

## 解决图片显示和typora显示

安装hexo-asset-image，使用相对路径

```
npm install hexo-asset-image --save
```

## 文章路径显示为固定的加密字符串

安装hexo-abbrlink

```
npm install hexo-abbrlink --save
```

在hexo根目录下的_config.yml中，修改为如下代码

```
permalink: posts/:abbrlink.html
abbrlink:
  alg: crc32  # 算法：crc16(default) and crc32
  rep: hex    # 进制：dec(default) and hex
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks
```



## hexo-abbrlink和hexo-asset-image冲突导致图片显示不出来

- 打开```\node_modules\hexo-asset-image\index.js```
- 修改```var endPos = link.length-1;```为```var endPos = link.length-5;```

## 添加PDF链接

- ```
    npm install --save hexo-pdf
    ```

- 在source文件夹下新建一个books文件夹，并将PDF文件放入

- 使用方法：在markdown文件中使用链接，譬如```[Future-and-Indoor-Networks](/books/Future-and-Indoor-Networks.pdf)```