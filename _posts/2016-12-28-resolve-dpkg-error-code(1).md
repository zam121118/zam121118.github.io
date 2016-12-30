---
layout: post
title:  "dpkg安装deb包返回error code (1)"
date:   2016-12-28 15:57:28
categories: Linux
tags: Linux
author: Amy Zhong
---


* content
{:toc}


dpkg安装deb包返回error code (1)




在我的环境中 `Xenial` ，每次运行 `sudo apt -y update && sudo apt -y upgrade` 的过程中，总是忘记去关掉正在运行的`VBox Win10`, 所以每次升级都会弹出来提醒我手动关掉。
手动关掉了，但是`apt`对新版本的`VirtualBox`解包后，安装就会报类似的错误：

```sh
。。。。。。（此处省略1万中）

正准备解包 .../virtualbox-5.1_5.1.12-112440~Ubuntu~xenial_amd64.deb  ...
dpkg：警告：子进程 旧的 pre-removal 脚本 返回错误状态 1
dpkg: 现在尝试使用新软件包所带的脚本...
dpkg: 处理归档 /var/cache/apt/archives/virtualbox-5.1_5.1.12-112440~Ubuntu~xenial_amd64.deb (--unpack)时出错：
 子进程 新的 pre-removal 脚本 返回错误状态 1
A copy of VirtualBox is currently running.  Please close it and try again.

。。。。。。。（再省略n万字）

在处理时有错误发生：
 /var/cache/apt/archives/virtualbox-5.1_5.1.12-112440~Ubuntu~xenial_amd64.deb
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

注意对于这一类的错误 `E: Sub-process /usr/bin/dpkg returned an error code (1)`， 通用解决方法：

```sh
$ sudo dpkg -i --force-overwrite /var/cache/apt/archives/*.deb
```
**NOTE:** 此处 `*.deb`指的就是当时报错的包，复制于此并回车，Okay啦～～～～

GoodDay :)
