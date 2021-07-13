---
title: VSCODE插件
tags:
  - vscode
originContent: ''
categories:
  - vscode
toc: false
abbrlink: e9b78b5f
date: 2016-07-13 20:04:52
---

Bracket Pair Colorizer  括号颜色
c/c++ 支持
Chinese 中文支持
vscode-icons 设置文件图标主题
translate to chinese 翻译
下载字体：https://github.com/tonsky/FiraCode
把下载的压缩包解压，然后找到ttf文件夹，全部选中，右键安装即可

```
  "editor.fontFamily": "'Fira Code', Consolas, 'Courier New', monospace",//添加上FiraCode字体
  "editor.fontLigatures": true,//开启连体字
```
如果配置完后，编辑器的内容没什么变化的话，重启一下就可以了

# VisualStudio代码无法监视此大工作区中的文件更改”(错误ENOSPC)
当您看到此通知时，它指示VS代码文件监视程序正在耗尽句柄，因为工作区很大，并且包含许多文件。运行以下命令可以查看当前限制：

```
cat /proc/sys/fs/inotify/max_user_watches
```
通过编辑，可以将限制提高到其最大值。/etc/sysctl.conf并将这一行添加到文件的末尾：

```
#打开
sudo vim /etc/sysctl.conf
```

```
#添加该代码
fs.inotify.max_user_watches=524288
```

```
#保存退出运行
sudo sysctl -p
```
