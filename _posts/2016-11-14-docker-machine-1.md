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


## Docker-machine 常用命令

`alias dm=‘docker-machine’`

- dm ls
- dm create

```bash
## $ dm create --driver virtualbox(linux)/hyper-v(win)/hyperkit(mac) vm_name
$ dm ls
NAME   ACTIVE   DRIVER   STATE   URL   SWARM   DOCKER   ERRORS

$ dm create --driver virtualbox default
Running pre-create checks...
(default) Default Boot2Docker ISO is out-of-date, downloading the latest release...
(default) Latest release for github.com/boot2docker/boot2docker is v1.12.5
(default) Downloading /home/amy/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v1.12.5/boot2docker.iso...
(default) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
Creating machine...
(default) Copying /home/amy/.docker/machine/cache/boot2docker.iso to /home/amy/.docker/machine/machines/default/boot2docker.iso...
(default) Creating VirtualBox VM...
(default) Creating SSH key...
(default) Starting the VM...
(default) Check network to re-create if needed...
(default) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env default

$ dm env default
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/amy/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
export DOCKER_API_VERSION="1.24"
# Run this command to configure your shell:
# eval $(docker-machine env default)

$ dm ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           v1.12.5
```

`docker-machine create` 会下载一个轻量级且安装了`docker engine`的 linux发行版镜像[boot2docker.iso]并在你新创VM上启动。
