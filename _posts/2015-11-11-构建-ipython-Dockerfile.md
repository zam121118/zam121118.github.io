---    
layout: post    
title: "dockerfile 构建 ipython docker image"    
date: 2015-11-11 21:20:57    
categories: Dockerfile    
tags: Docker  Dockerfile Linux    
author: Amy Zhong    
---    

* content    
{:toc}

编写dockerfile构建ipython的Docker image




## ipython的dockerfile

```
FROM ubuntu:latest

MAINTAINER amy zam121118@163.com

#RUN mkdir -p /opt/anaconda2/

COPY Anaconda2-2.4.1-Linux-x86_64.sh /opt
RUN cd /opt && /bin/bash Anaconda2-2.4.1-Linux-x86_64.sh -b -f

ENV PATH /root/anaconda2/bin:/usr/bin/python:$PATH
#RUN source ~/.bashrc

EXPOSE 81

CMD /bin/bash

```