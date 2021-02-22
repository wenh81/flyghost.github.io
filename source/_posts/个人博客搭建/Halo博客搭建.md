---
title: Halo博客搭建
date: 2021-01-05 00:00:00
swiper: true
swiperImg: '/medias/5.jpg'
top: true
tags: 博客
---
# halo博客搭建

## 1、介绍

Halo 的整个应用程序只有一个 Jar 包，且不包含用户的任何配置，它放在任何目录都是可行的。需要注意的是，Halo 的整个额外文件全部存放在 `~/.halo` 目录下，包括 `application.yaml（用户配置文件）`，`template/themes（主题目录）`，`upload（附件上传目录）`，`halo.db.mv（数据库文件）`。一定要保证 `~/.halo` 的存在，你博客的所有资料可都存在里面。所以你完全不需要担心安装包的安危，它仅仅是个服务而已。

## 2、安装OpenJDK11

```bash
sudo apt install openjdk-11-jdk
```

## 3、安装halo

- 新建路径

```bash
mkdir halo
```

- 下载最新的 Halo 安装包，{{version}} 为版本号，不带 v，更多下载地址请访问 https://halo.run/archives/download.html

```bash
wget https://github.com/halo-dev/halo/releases/download/v1.4.2/halo-1.4.2.jar -O halo-latest.jar
```

- 下载配置文件到 ~/.halo 目录

```bash
curl -o ~/.halo/application.yaml --create-dirs https://dl.halo.run/config/application-template.yaml
```

- 修改配置文件

```bash
vim ~/.halo/application.yaml
```

得到如下配置

```yaml
server:
  port: 8090  # 端口

  # Response data gzip.
  compression:
    enabled: false
spring:
  datasource:

    # H2 数据库配置
    driver-class-name: org.h2.Driver
    url: jdbc:h2:file:~/.halo/db/halo
    username: creekwater
    password: yang0517

    # MySQL database configuration.
#    driver-class-name: com.mysql.cj.jdbc.Driver
#    url: jdbc:mysql://127.0.0.1:3306/halodb?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
#    username: root
#    password: 123456

  # H2 database console configuration.
  h2:
    console:
      settings:
        web-allow-others: false
      path: /h2-console
      enabled: false

halo:
  # web后台路径 https://your-domain/{admin-path}
  # https://127.0.0.1/creekwater-admin
  admin-path: creekwater-admin

  # memory or level
  cache: memory
```

1. 如果需要自定义端口，修改 `server` 节点下的 `port` 即可。
2. 默认使用的是 `H2 Database` 数据库，这是一种嵌入式的数据库，使用起来非常方便。需要注意的是，默认的用户名和密码为 `admin` 和 `123456`，这个是自定义的，最好将其修改，并妥善保存。
3. 如果需要使用 `MySQL` 数据库，需要将 `H2 Database` 的所有相关配置都注释掉，并取消 `MySQL` 的相关配置。另外，`MySQL` 的默认数据库名为 `halodb`，请自行配置 `MySQL` 并创建数据库，以及修改配置文件中的用户名和密码。
4. `h2` 节点为 `H2 Database` 的控制台配置，默认是关闭的，如需使用请将 `h2.console.settings.web-allow-others` 和 `h2.console.enabled` 设置为 `true`。控制台地址即为 `域名/h2-console`。注意：非紧急情况，不建议开启该配置。
5. `server.compression.enabled` 为 `Gzip` 功能配置，如有需要请设置为 `true`，需要注意的是，如果你使用 `Nginx` 或者 `Caddy` 进行反向代理的话，默认是有开启 `Gzip` 的，所以这里可以保持默认。
6. `halo.admin-path` 为后台管理的根路径，默认为 `admin`，如果你害怕别人猜出来默认的 `admin`（就算猜出来，对方什么都做不了），请自行设置。仅支持一级，且前后不带 `/`。
7. `halo.cache` 为系统缓存形式的配置，可选 `memory` 和 `level`，默认为 `memory`，将数据缓存到内存，使用该方式的话，重启应用会导致缓存清空。如果选择 `level`，则会将数据缓存到磁盘，重启不会清空缓存。如不知道如何选择，建议默认。

> 注意
>
> 使用 MySQL 之前，必须要先新建一个 `halodb` 数据库，MySQL 版本需 5.7 以上。
>
> ```sql
> create database halodb character set utf8mb4 collate utf8mb4_bin;
> ```

- 启动测试

``` 
java -jar halo-latest.jar
```

- 看到以下输出，说明启动成功

```bash
run.halo.app.listener.StartedListener    : Halo started at         http://127.0.0.1:8090
run.halo.app.listener.StartedListener    : Halo admin started at   http://127.0.0.1:8090/admin
run.halo.app.listener.StartedListener    : Halo has started successfully!
```

## 4、配置halo开机自启动

上面我们已经完成了 Halo 的整个配置和安装过程，接下来我们对其进行更完善的配置，比如：`需要开机自启？`，`更简单的启动方式？`

实现以上功能我们只需要新增一个配置文件即可，也就是使用 `Systemd` 来完成这些工作。

- 下载 Halo 官方的 halo.service 模板

```bash
sudo curl -o /etc/systemd/system/halo.service --create-dirs https://dl.halo.run/config/halo.service
```


- 修改 halo.service

```bash
sudo vim /etc/systemd/system/halo.service
```

```conf
[Unit]
Description=Halo Service
Documentation=https://halo.run
After=network-online.target
Wants=network-online.target

[Service]
User=wzy
Type=simple
ExecStart=/usr/bin/java -server -Xms256m -Xmx256m -jar YOUR_JAR_PATH
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=always
StandOutput=syslog

StandError=inherit

[Install]
WantedBy=multi-user.target
```

- -Xms256m：为 JVM 启动时分配的内存，请按照服务器的内存做适当调整，512 M 内存的服务器推荐设置为 128，1G 内存的服务器推荐设置为 256，默认为 256。
- -Xmx256m：为 JVM 运行过程中分配的最大内存，配置同上。
- YOUR_JAR_PATH：Halo 安装包的绝对路径，例如 `/www/wwwroot/halo-latest.jar`。

> -Xms256m：为 JVM 启动时分配的内存，请按照服务器的内存做适当调整，512 M 内存的服务器推荐设置为 128，1G 内存的服务器推荐设置为 256，默认为 256。
>
> -Xmx256m：为 JVM 运行过程中分配的最大内存，配置同上。
>
> YOUR_JAR_PATH：Halo 安装包的绝对路径，例如 `/www/wwwroot/halo-latest.jar`。

提示

1. 如果你不是按照上面的方法安装的 JDK，请确保 `/usr/bin/java` 是正确无误的。
2. systemd 中的所有路径均要写为绝对路径，另外，`~` 在 systemd 中也是无法被识别的，所以你不能写成类似 `~/halo-latest.jar` 这种路径。
3. 如何检验是否修改正确：把 ExecStart 中的命令拿出来执行一遍。

```bash
# 修改 service 文件之后需要刷新 Systemd
sudo systemctl daemon-reload

# 使 Halo 开机自启
sudo systemctl enable halo

# 启动 Halo
sudo service halo start

# 重启 Halo
sudo service halo restart

# 停止 Halo
sudo service halo stop

# 查看 Halo 的运行状态
sudo service halo status
```