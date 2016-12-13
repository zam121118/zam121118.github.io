---
layout: post
title: 初始Docker-machine
date: 2016-12-11 15:35:27
categories: Docker
tags: Docker Docker进阶
author: Amy Zhong
---

* content
{:toc}


写一点点关于Docker-machine初始印象。





## 我们常说的Docker

其实，常说的 Docker 指的是 `Docker-Engine`, 即 Client-Server 结构的应用：由Client端通过 REST API 接口（Docker CLI，eg: docker run <image>, .... ）与 Docker daemon 进行任务定义，此处Docker server即为docker daemon。

用一张docker官方文档图片说明比较好：

![Docker-Engine](https://docs.docker.com/machine/img/engine.png)

## 说说Docker-Machine

关于安装、命令补全、提示符定义等很简单，直接参照docs.docker.com就行了。

先上一张 Docker-Machine 的图片：

![Docker-Machine](https://docs.docker.com/machine/img/machine.png)

应该说 Docker-Machine 就是一个管理远程 dockerized hosts 或者本地 dockerized virtual-hosts 的工具，
可以方便的在你指定的远程 hosts 上安装docker engine, 或者在本地，依据你指定的虚拟化驱动（Oracle VirtualBox, micro Hyper-V等）在你本地宿主机上创建virtual-hosts, 下载并安装`boot2docker.iso`镜像（非常轻量级且预装docker engine的主机镜像），通过 Docker-Machine CLI 配置管理以及连接到remote/local dockerized hosts上，再通过  Docker CLI 指定 docker daemon 要运行的容器服务。

就这么简单，不用你自己一个个登录远程服务器，按照官方安装指令，一个一个进行。

所以说 docker 开源社区这么火热， machine、compose、swarm 这 3 宝是决定性的啊！

## 之前有人这么说

我觉得人家说的简单易懂，本来想贴出来，想想算啦。 国内的博客、技术网站就是这样 你抄我， 我抄他， 他抄官方文档。。。。。


算了，上手一段时间，再来写自己的经验吧。。。。（see u next :）
