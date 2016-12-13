#To Record some Dockerfile and Usage

[TOC]

Date: 2016-11-17

## Base image

Highly recommend `Alpine:latest`, It has a safe and customized [Packages Source](http://dl-cdn.alpinelinux.org/alpine/v3.3/main/x86_64/)

### Add `bash`

Alpine_linux use `/bin/sh` defaultly, so If you want use `/bin/bash` in your Dockerfile, Just add the `bash` to your `Alpine:latest` base image (pulling from [hub.docker.com](https://hub.docker.com/_/alpine/)).

**NOTE:** Please remember *one* process *one* docker container when building yourself image!!!

Here are Dockerfile likes:

```sh
FROM alpine:latest

MAINTAINER maorongrong 121075257@qq.com

RUN apk add --no-cache --update-cache bash

CMD ["/bin/bash"]
```

Then, build likes this:

```sh
$ docker build -t alpine-bash:latest .
```

Here `.` represent the dir which your Dockerfile placing in.


### Add glibc

Because many application likes java, python, etc... need glibc libraries(`C`,`C++`...), and Alpine linux use musl libraries defaultly !!! 

If you want install some packages with glibc, so you need to add glibc additionally. maybe you [can see this](http://wiki.alpinelinux.org/wiki/Running_glibc_programs).

**NOTE:**  We can add glibc to our base `Alpine:latest`,too! thank the project [`sgerrand/alpine-pkg-glibc
`](https://github.com/sgerrand/alpine-pkg-glibc), we can add glibc likes this:


```sh
FROM alpine-bash:latest

MAINTAINER maorongrong 121075257@qq.com

RUN apk --no-cache add ca-certificates
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub
RUN wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.23-r3/glibc-2.23-r3.apk
RUN apk add glibc-2.23-r3.apk

CMD ["/bin/bash"]
```

Then, build likes this:

```sh
$ docker build -t alpine-glibc:latest .
```

Here `.` represent the dir which your Dockerfile placing in, too.

The installing procedure may like this:

```sh
Sending build context to Docker daemon 3.072 kB
Step 1 : FROM alpine:v0.1
 ---> 089242968f18
Step 2 : MAINTAINER maorongrong 121075257@qq.com
 ---> Running in 264022df3165
 ---> 24ed79fac05f
Removing intermediate container 264022df3165
Step 3 : RUN apk --no-cache add ca-certificates
 ---> Running in ec075123375e
fetch http://dl-cdn.alpinelinux.org/alpine/v3.3/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.3/community/x86_64/APKINDEX.tar.gz
(1/2) Installing openssl (1.0.2j-r0)
(2/2) Installing ca-certificates (20160104-r2)
Executing busybox-1.24.2-r0.trigger
Executing ca-certificates-20160104-r2.trigger
OK: 14 MiB in 18 packages
 ---> 1d6c935808ec
Removing intermediate container ec075123375e
Step 4 : RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub
 ---> Running in d173d7ea983e
 ---> 3a403f53e3b5
Removing intermediate container d173d7ea983e
Step 5 : RUN wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.23-r3/glibc-2.23-r3.apk
 ---> Running in 62247dfe6488
Connecting to github.com (192.30.253.112:443)
Connecting to github-cloud.s3.amazonaws.com (54.231.82.146:443)
glibc-2.23-r3.apk    100% |*******************************|  2874k  0:00:00 ETA
 ---> 110b493bcbf2
Removing intermediate container 62247dfe6488
Step 6 : RUN apk add glibc-2.23-r3.apk
 ---> Running in 57f723a1a5d2
(1/1) Installing glibc (2.23-r3)
OK: 18 MiB in 19 packages
 ---> 309bb1715dff
Removing intermediate container 57f723a1a5d2
Step 7 : CMD /bin/bash
 ---> Running in b9c37cb995b1
 ---> 8a5ab5848b23
Removing intermediate container b9c37cb995b1
Successfully built 8a5ab5848b23
```

Congratulations!!

## Build your `Jupyter notebook` with `alpine`

Check the size of the `Jupyter` offical docker images:

```sh
cloud@cloud-m3-01:~$ docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
jupyterhub/jupyterhub          latest              70f595fdcc2a        16 hours ago        667.2 MB
python                         latest              a399f3670068        6 days ago          682.5 MB
gcc                            latest              293aa389a05e        6 days ago          1.483 GB
redis                          latest              5f515359c7f8        8 days ago          182.9 MB
mysql                          latest              cd88b71c6c8c        8 days ago          383.4 MB
golang                         latest              ef15416724f6        8 days ago          672.4 MB
mariadb                        latest              66498efd6bd8        8 days ago          389.9 MB
java                           latest              861e95c114d6        8 days ago          643.2 MB
debian                         latest              73e72bf822ca        9 days ago          123 MB
centos                         latest              0584b3d2cf6d        2 weeks ago         196.5 MB
jupyter/all-spark-notebook     latest              96139b6593c5        3 weeks ago         4.791 GB
jupyter/datascience-notebook   latest              357fc20fb373        3 weeks ago         4.797 GB
alpine                         latest              baa5d63471ea        4 weeks ago         4.803 MB
busybox                        latest              e02e811dd08f        5 weeks ago         1.093 MB
```

First to look the `SIZE`, how big there offical images are! They should be smaller than now !!!! 

The smallest is `busybox` and `alpine`, look there words:

>What is Alpine Linux?
>
>Alpine Linux is a Linux distribution built around musl libc and BusyBox. The image is only 5 MB in size and has access to a package repository that is much more complete than other BusyBox based images. This makes Alpine Linux a great image base for utilities and even production applications. Read more about Alpine Linux here and you can see how their mantra fits in right at home with Docker images.

### pip install juoyter notebook in alpine

Now try to build `jupyter notebook` by installing `pip` yourself to Dockerfile:

```sh
FROM alpine-glibc:latest

MAINTAINER maorongrong 121075257@qq.com

ADD run.sh /run.sh
RUN chmod 755 /*.sh

RUN apk add --no-cache --update-cache libc-dev gcc g++
RUN apk add --no-cache --update-cache py-pip python python-dev
RUN pip install --upgrade pip
RUN pip install jupyter

EXPOSE 8888 

VOLUME /notebooks

WORKDIR /notebooks

CMD ["/run.sh"]
```

`run.sh` have written previously:

```sh
#!/bin/bash
/usr/bin/jupyter notebook
```

### errors I ever encounterd making me exhausted

when first install, I encounter many errors

```sh
    copying zmq/utils/ipcmaxlen.h -> build/lib.linux-x86_64-2.7/zmq/utils
    copying zmq/devices/monitoredqueue.pxd -> build/lib.linux-x86_64-2.7/zmq/devices
    running build_ext
    running configure
    Did not find libzmq via pkg-config.
    {'libraries': ['zmq'], 'extra_link_args': [], 'runtime_library_dirs': [], 'library_dirs': [], 'include_dirs': []}
    gcc -fno-strict-aliasing -Os -fomit-frame-pointer -DNDEBUG -Os -fomit-frame-pointer -fPIC -c build/temp.linux-x86_64-2.7/scratch/check_sys_un.c -o build/temp.linux-x86_64-2.7/scratch/check_sys_un.o
    build/temp.linux-x86_64-2.7/scratch/check_sys_un.c:1:19: fatal error: stdio.h: No such file or directory
    compilation terminated.
    Warning: No sys/un.h, IPC_PATH_MAX_LEN will be undefined: command 'gcc' failed with exit status 1
    ************************************************
    Configure: Autodetecting ZMQ settings...
        Custom ZMQ dir:
    creating build/temp.linux-x86_64-2.7/scratch/tmp
    cc -c /tmp/timer_createeqO4CL.c -o build/temp.linux-x86_64-2.7/scratch/tmp/timer_createeqO4CL.o
...........
    gcc -fno-strict-aliasing -Os -fomit-frame-pointer -DNDEBUG -Os -fomit-frame-pointer -fPIC -DZMQ_HAVE_CURVE=1 -DZMQ_USE_TWEETNACL=1 -DZMQ_USE_EPOLL=1 -Ibundled/zeromq/include -Ibundled/zeromq/tweetnacl/src -Ibundled/zeromq/tweetnacl/contrib/randombytes -Ibundled -I/usr/include/python2.7 -c buildutils/initlibzmq.c -o build/temp.linux-x86_64-2.7/buildutils/initlibzmq.o
    In file included from buildutils/initlibzmq.c:10:0:
1.    /usr/include/python2.7/Python.h:19:20: fatal error: limits.h: No such file or directory
    compilation terminated.
    error: command 'gcc' failed with exit status 1
    
    ----------------------------------------
Command "/usr/bin/python -u -c "import setuptools, tokenize;__file__='/tmp/pip-build-IkoiV3/pyzmq/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record /tmp/pip-ZWs_yJ-record/install-record.txt --single-version-externally-managed --compil

2. /usr/include/python2.7/Python.h:19:20: fatal error: stdio.h: No such file or directory
    compilation terminated.
    error: command 'gcc' failed with exit status 1

3.  gcc: error trying to exec ‘cc1plus’: execvp: No such file or directory 

..............

```

so to figure out all these errors, I try to do these:

- `limits.h: No such file or directory`

    `apk add --no-cache --update-cache linux-headers gcc`



- `gcc: error trying to exec ‘cc1plus’: execvp: No such file or directory `

    `apk add --no-cache --update-cache g++`


- `GCC fatal error: stdio.h: No such file or directory`

    `apk add --no-cache --update-cache libc-dev`


###  use it now

you can do like this:
```sh
$ docker run -d --restart=always -p 2048:8888 -v /home/amy/python_code:/notebooks --name jupyter jupyter/notebook-alpine:latest

$ docker logs jupyter
```