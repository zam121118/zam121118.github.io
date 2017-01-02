---
layout: post
title:  Docker生态圈--服务发现
date:   2016-11-25
categories:  Docker
tags:  Docker
---

* content
{:toc}

服务发现，键值存储机制是docker集群管理最主要的组件。





## 一些流行的服务发现工具和相关项目：

- [etcd](https://www.digitalocean.com/community/tutorials/how-to-use-etcdctl-and-etcd-coreos-s-distributed-key-value-store)：服务发现/全局分布式键值对存储
- [consul](https://www.digitalocean.com/community/tutorials/an-introduction-to-using-consul-a-service-discovery-system-on-ubuntu-14-04)：服务发现/全局分布式键值对存储
- [zookeeper](https://www.digitalocean.com/community/tutorials/an-introduction-to-mesosphere#a-basic-overview-of-apache-mesos)：服务发现/全局分布式键值对存储
- [crypt](http://xordataexchange.github.io/crypt/)：加密etcd条目的项目
- [confd](https://www.digitalocean.com/community/tutorials/how-to-use-confd-and-etcd-to-dynamically-reconfigure-services-in-coreos)：观测键值对存储变更和新值的触发器重新配置服务
