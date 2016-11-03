# 搭建Docker仓库（1）

标签（空格分隔）： docker 云计算组项目

---

搭建Docker仓库，容器的第一步。教育网用户可将源换为清华园。

## 参考

安装 `docker`可参看[官网document](https://docs.docker.com/engine/installation/linux/ubuntulinux/)
[Docker私有仓库Registry搭建](http://www.open-open.com/lib/view/open1456666305640.html)
[部署私有Docker Registry](http://www.cnblogs.com/xcloudbiz/articles/5526262.html)

## server环境：

`IP：X.X.X.167`

* 查看系统：

```
cloud@cloud-m3-01:~$ uname -a
Linux cloud-m3-01 3.19.0-58-generic #64~14.04.1-Ubuntu SMP Fri Mar 18 19:05:43 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
cloud@cloud-m3-01:~$ cat /etc/issue
Ubuntu 14.04.5 LTS \n \l
cloud@cloud-m3-01:~$ hostname
cloud-m3-01
```

* 查看资源：

```
cloud@cloud-m3-01:~$ free -h
             total       used       free     shared    buffers     cached
Mem:           39G       3.9G        35G       9.8M       275M       2.5G
-/+ buffers/cache:       1.2G        38G
Swap:          39G         0B        39G
cloud@cloud-m3-01:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev             20G   12K   20G   1% /dev
tmpfs           4.0G  1.4M  4.0G   1% /run
/dev/dm-0       875G   27G  804G   4% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
none            5.0M     0  5.0M   0% /run/lock
none             20G  148K   20G   1% /run/shm
none            100M   40K  100M   1% /run/user
/dev/sde2       237M   96M  129M  43% /boot
/dev/sde1       511M  3.4M  508M   1% /boot/efi
```
* 查看CPU

应该是2个Cpu,每个Cpu有6个core,应该是Intel的U,支持超线程,所以显示24个逻辑CPU
```
查看物理CPU的个数
cloud@cloud-m3-01:~$ cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l
2

查看逻辑CPU的个数
cloud@cloud-m3-01:~$ cat /proc/cpuinfo | grep "processor"| wc -l
24

查看CPU是几核
cloud@cloud-m3-01:~$ cat /proc/cpuinfo | grep "cores" | uniq
cpu cores	: 6

```

## 安装docker
ubuntu安装docker请参考官方文档

## 启动仓库容器

```
cloud@cloud-m3-01:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
registry            latest              c6c14b3960bd        5 weeks ago         33.31 MB
cloud@cloud-m3-01:~$ docker run -d -v /registry:/var/lib/registry -p 5000:5000 --restart=always --name registry registry
1044750b2543af320d40759455e539311557ab89dfe29e74fdf2c06fa2514b75
cloud@cloud-m3-01:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
1044750b2543        registry            "/entrypoint.sh /etc/"   3 minutes ago       Up 3 minutes        0.0.0.0:5000->5000/tcp   registry

```
Registry容器逻辑上将镜像保存在容器内/var/lib/registry，实际保存server的/registry目录了。


### 从docker Hub下载镜像放在本地

```
cloud@cloud-m3-01:~$ docker search -s 100  ubuntu
Flag --stars has been deprecated, use --filter=stars=3 instead
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ubuntu    Ubuntu is a Debian-based Linux operating s...   4649      [OK]       
cloud@cloud-m3-01:~$ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
952132ac251a: Pull complete 
82659f8f1b76: Pull complete 
c19118ca682d: Pull complete 
8296858250fe: Pull complete 
24e0251a0e2c: Pull complete 
Digest: sha256:f4691c96e6bbaa99d99ebafd9af1b68ace2aa2128ae95a60369c506dd6e6f6ab
Status: Downloaded newer image for ubuntu:latest
```
### 下载的镜像打个tag准备推送到本地
```
cloud@cloud-m3-01:~$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
cloud@cloud-m3-01:~$ docker tag registry:latest 127.0.0.1:5000/registry:latest
cloud@cloud-m3-01:~$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
127.0.0.1:5000/ubuntu     latest              bd3d4369aebc        12 days ago         126.6 MB
ubuntu                    latest              bd3d4369aebc        12 days ago         126.6 MB
127.0.0.1:5000/registry   latest              c6c14b3960bd        5 weeks ago         33.31 MB
registry                  latest              c6c14b3960bd        5 weeks ago         33.31 MB

```

### 推送到私有仓库

```
cloud@cloud-m3-01:~$ docker push 127.0.0.1:5000/ubuntu:latest
The push refers to a repository [127.0.0.1:5000/ubuntu]
0cad5e07ba33: Pushed 
48373480614b: Pushed 
055757a19384: Pushed 
c6f2b330b60c: Pushed 
c8a75145fcc4: Pushed 
latest: digest: sha256:463b797bb7cb49e6df205d51479425adbac1d122dac5922cf6bad9277fe7dfbe size: 1357
cloud@cloud-m3-01:~$ docker push 127.0.0.1:5000/registry:latest
The push refers to a repository [127.0.0.1:5000/registry]
3bb5bc5ad373: Pushed 
35039a507f7a: Pushed 
d00444e19d65: Pushed 
aa3a31ee27f3: Pushed 
4fe15f8d0ae6: Pushed 
latest: digest: sha256:51d8869caea35f58dd6a2309423ec5382f19c4e649b5d2c0e3898493f42289d6 size: 1363
```

### 查看server本地文件夹


* 查看本地文件夹下的镜像

```
cloud@cloud-m3-01:/registry/docker/registry/v2/repositories$ ls
registry  ubuntu
```

* 访问本server5000端口查看仓库

```
cloud@cloud-m3-01:/registry/docker/registry/v2/repositories$ curl http://127.0.0.1:5000/v2/_catalog
{"repositories":["registry","ubuntu"]}
```
## 验证仓库

### 删除本地镜像

```
cloud@cloud-m3-01:~$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
127.0.0.1:5000/ubuntu     latest              bd3d4369aebc        12 days ago         126.6 MB
ubuntu                    latest              bd3d4369aebc        12 days ago         126.6 MB
127.0.0.1:5000/registry   latest              c6c14b3960bd        5 weeks ago         33.31 MB
registry                  latest              c6c14b3960bd        5 weeks ago         33.31 MB
cloud@cloud-m3-01:~$ docker rmi ubuntu:latest
cloud@cloud-m3-01:~$ docker rmi 127.0.0.1:5000/registry
cloud@cloud-m3-01:~$ docker rmi 127.0.0.1:5000/ubuntu
cloud@cloud-m3-01:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
registry            latest              c6c14b3960bd        5 weeks ago         33.31 MB
```
仓库只剩registry镜像，注意仓库容器正在使用该镜像，不要删除，也不要强制删除。

### 从咱的仓库127.0.0.1:5000获取镜像
```
cloud@cloud-m3-01:~$ docker pull 127.0.0.1:5000/ubuntu:latest
latest: Pulling from ubuntu
952132ac251a: Pull complete 
82659f8f1b76: Pull complete 
c19118ca682d: Pull complete 
8296858250fe: Pull complete 
24e0251a0e2c: Pull complete 
Digest: sha256:463b797bb7cb49e6df205d51479425adbac1d122dac5922cf6bad9277fe7dfbe
Status: Downloaded newer image for 127.0.0.1:5000/ubuntu:latest
cloud@cloud-m3-01:~$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
127.0.0.1:5000/ubuntu   latest              bd3d4369aebc        12 days ago         126.6 MB
registry                latest              c6c14b3960bd        5 weeks ago         33.31 MB
```
仓库搭建告一段落，接下来尝试从其他机器访问该server下载镜像。


## 测试远程hosts从本server push/pull 镜像（insecure 模式）

> 要想在远程使用该 registry，就必须使用 TLS 来确保通信安全，就像使用 SSL 来配置 web 服务器。也可以强制 docker registry 运行在 insecure 模式，这种模式虽然配置起来要简单一些，但很不安全，一般不建议使用。

### 确保远程server到本机网络通

```
amy@ubuntu-host2:~$ ping 202.117.16.167
PING 202.117.16.167 (202.117.16.167) 56(84) bytes of data.
64 bytes from 202.117.16.167: icmp_seq=1 ttl=128 time=0.888 ms
64 bytes from 202.117.16.167: icmp_seq=2 ttl=128 time=1.33 ms
^C
--- 202.117.16.167 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.888/1.111/1.334/0.223 ms
```

### 为远程host daemon添加远程仓库

```
amy@ubuntu-host2:~$ sudo vim /etc/default/docker
添加：
DOCKER_OPTS="--insecure-registry XXX.XXX.XXX.167:5000"
#ADD_REGISTRY='--add-registry XXX.XXX.XXX.167:5000'  
#INSECURE_REGISTRY='--insecure-registry XXX.XXX.XXX.167:5000'  
```

### 重启hosts docker服务&测试

```
amy@ubuntu-host2:~$ sudo service docker restart
docker stop/waiting
docker start/running, process XXXX

amy@ubuntu-host2:~$ sudo docker pull XXX.XXX.XXX.167:5000/ubuntu:latest
latest: Pulling from ubuntu
436c176520d3: Pull complete 
67891c7e4743: Pull complete 
19d170efa81d: Pull complete 
fc1d374b7277: Pull complete 
b2a96566725c: Pull complete 
ae81bbda2b6c: Pull complete 
Digest: sha256:110f9122f773b4d4898a7921019b4faf5a33873b17062b07f009cdfde9f32d77
Status: Downloaded newer image for 202.117.16.167:5000/ubuntu:latest
```

**NOTE：** 在Private Registry2中查看或检索Repository或images，将不能用docker search：

```
$ docker search 10.10.105.71:5000/tonybai/busybox/
Error response from daemon: Unexpected status code 404
```

* 但通过v2版本的API，我们可以实现相同目的：

```
$curl  http://10.10.105.71:5000/v2/_catalog
{"repositories":["tonybai/busybox"]}

$ curl  http://10.10.105.71:5000/v2/tonybai/busybox/tags/list
{"name":"tonybai/busybox","tags":["latest"]}
```

**NOTE:** *将同一个标签名的新镜像`push`到仓库中，仓库会自动更新镜像及镜像ID，换言之取而代之。*

## Secure Registry

### Docker offical deploy

Docker官方是推荐你采用**Secure Registry**的工作模式的，即transport采用**TLS**。这样我们就需要为Registry配置TLS所需的key和crt文件了。

[Docker官方：Deploying a registry server](https://docs.docker.com/registry/deploying/)要求提供registry的server有一个域名，并从某知名CA签署获取证书。
`mkdir -p certs`将证书及秘钥复制于此`certs/domain.crt`,`certs/domain.key`.
并以```-v ~/certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key```参数启动registry container, 即为`Secure Registry`。

### 自签证书搭建sercure registry

可以参考docker官方给出的文件：[Using self-signed certificates](https://docs.docker.com/registry/insecure/)

* SSL/TLS
先谈一谈TLS，或者old name - SSL，及其加密方式。
[SSL/TLS协议运行机制的概述](http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)
[图解SSL/TLS协议](http://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html)


木有指向该server的独立域名，也无法获得知名CA签发的证书，那就自签证书。
[基于OpenSSL自建CA和颁发SSL证书](http://blog.csdn.net/xp5xp6/article/details/52344291)
[ubuntu下自我签名数字证书](http://blog.csdn.net/chen_jianjian/article/details/47336683)

[主要参考：Secure Registry](http://www.cnblogs.com/xcloudbiz/articles/5526262.html)

* 自签证书

```
amy@ubuntu-host2:~$ sudo mkdir -p `pwd`/certs && sudo openssl req -newkey rsa:2048 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
[sudo] password for amy: 
Generating a 2048 bit RSA private key
.........+++
....................................................................................+++
writing new private key to 'certs/domain.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Shanxi
Locality Name (eg, city) []:Xi'an                 
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Xi'an Jiao Tong University
Organizational Unit Name (eg, section) []:Computer Science
Common Name (e.g. server FQDN or YOUR name) []:mydockerhub.com       
Email Address []:121075257@qq.com

amy@ubuntu-host2:~/certs$ ls
domain.crt  domain.key
```

* 修改hosts

证书的Common Name:mydockerhub.com，我们需要修改一下/etc/hosts文件:
`XXX.XXX.XXX.167 mydockerhub.com` **(要远程该仓库的Host也要改)**

* 使得远程docker daemon信任该证书

   远程docker daemon需要安装我们自己签发的CA证书：

```
$(registry host) sudo mkdir -p /etc/docker/certs.d/mydockerhub.com:5000
$(registry host) sudo cp certs/domain.crt /etc/docker/certs.d/mydockerhub.com:5000/ca.crt
$(registry host) sudo service docker restart //安装证书后，重启Docker Daemon
```

   为远程docker daemon添加域名解析

```
vim /etc/hosts
追加：
XXX.XXX.XXX.167 mydockerhub.com
```

   为远程host daemon添加远程仓库**(不需要添加啊)**

```
amy@ubuntu-host2:~$ sudo vim /etc/default/docker
添加：
DOCKER_OPTS="--insecure-registry XXX.XXX.XXX.167:5000"
#ADD_REGISTRY='--add-registry XXX.XXX.XXX.167:5000'  
#INSECURE_REGISTRY='--insecure-registry XXX.XXX.XXX.167:5000'  
```

* Registry的鉴权管理

在Register server上，为Registry增加foo用户，密码foo123

```
amy@ubuntu-host2:~$ mkdir -p auth
amy@ubuntu-host2:~$ docker run --entrypoint htpasswd registry:latest -Bbn Cloud xjtudlc > auth/htpassswd

amy@ubuntu-host2:~$ ls auth/
htpasswd
```
* 启动encrty TLS registry并login
```
docker run -d -p 5000:5000 --restart=always --name registry \
  -v `pwd`/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v `pwd`/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2

docker login myregistrydomain.com:5000
```

## 仓库WEB监控

[ 根据开源项目：mkuchin/docker-registry-web搭建](https://github.com/mkuchin/docker-registry-web)

###  无认证方式

```
cloud@cloud-m3-01:~$ docker run -d -p 5000:5000 -v /registry:/var/lib/registry --name registry-srv registry:2
f00c015d45454d7dfefaf46dd7191f480df47acb0d47342d5270134b86066f1c

cloud@cloud-m3-01:~$ docker run -it -p 8443:8080 --name registry-web --link registry-srv -e REGISTRY_URL=http://registry-srv:5000/v2 -e REGISTRY_NAME=localhost:5000 hyper/docker-registry-web

CATALINA_OPTS: -Djava.security.egd=file:/dev/./urandom -Dcontext.path=
Using CATALINA_BASE:   /var/lib/tomcat7
Using CATALINA_HOME:   /usr/share/tomcat7
Using CATALINA_TMPDIR: /var/lib/tomcat7/temp
Using JRE_HOME:        /usr/lib/jvm/java-7-openjdk-amd64
Using CLASSPATH:       /usr/share/tomcat7/bin/bootstrap.jar:/usr/share/tomcat7/bin/tomcat-juli.jar
Nov 03, 2016 2:30:31 AM org.apache.coyote.AbstractProtocol init
INFO: Initializing ProtocolHandler ["http-bio-8080"]
Nov 03, 2016 2:30:31 AM org.apache.catalina.startup.Catalina load
INFO: Initialization processed in 563 ms
Nov 03, 2016 2:30:31 AM org.apache.catalina.core.StandardService startInternal
INFO: Starting service Catalina
Nov 03, 2016 2:30:31 AM org.apache.catalina.core.StandardEngine startInternal
INFO: Starting Servlet Engine: Apache Tomcat/7.0.52 (Ubuntu)
2016-11-03 02:30:44,142 [localhost-startStop-1] INFO  hibernate4.HibernatePluginSupport  - Set db generation strategy to 'update' for datasource DEFAULT

Configuring Spring Security Core ...
... finished configuring Spring Security Core
。。。。。。
Nov 03, 2016 2:30:52 AM org.apache.catalina.startup.Catalina start
INFO: Server startup in 21377 ms
```


## 认证方式

### start registry-srv:

```
cloud@cloud-m3-01:~$ docker run -d -p 5000:5000 --restart=always --name registry-srv \
>   -v /home/cloud/certs:/certs \
>   -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
>   -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
>   -v /registry:/var/lib/registry \
>   registry:2
f7769df82b27a62338a0d11140994f20396a67a33732a658976fb58ec1835d72
```

### start registry-web

```
cloud@cloud-m3-01:~$ docker run --restart=always -d -p 8443:8080 -v /home/cloud/certs:/certs --name registry-web --link registry-srv \
>            -e REGISTRY_URL=https://registry-srv:5000/v2 \
>            -e REGISTRY_TRUST_ANY_SSL=true \
>            -e REGISTRY_BASIC_AUTH=/certs/domain.crt \
>            -e REGISTRY_NAME=mydockerhub.com:5000 \
>            -e REGISTRY_READONLY=false \
>            hyper/docker-registry-web
458cd87550ba8234b5392d85ec107d18898d4a9faa75e5784d82e8e728d06e35
```

### check the containers

```
cloud@cloud-m3-01:~/certs$ docker ps -a
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                    NAMES
458cd87550ba        hyper/docker-registry-web   "start.sh"               4 seconds ago       Up 3 seconds        0.0.0.0:8443->8080/tcp   registry-web
f7769df82b27        registry:2                  "/entrypoint.sh /etc/"   22 seconds ago      Up 20 seconds       0.0.0.0:5000->5000/tcp   registry-srv

```

### 问题

参考Docker文件[《Registry Configuration Reference》](https://docs.docker.com/registry/configuration/#delete),应该可以通过书写`config.yml`配置文件启动registry。意味着在命令行启动`registry:2`时完全可以通过`-e REGISTRY_STORAGE_DELETE=true` 指定仓库中存储镜像的可删除性，并在`registry-web`中指定`-e REGISTRY_READONLY=false`,即可以直接在web端删除存储的镜像。

**BUT**: 事实却是
> Error deleting registry:latest: Deletion disabled in registry, more info(指向文章Registry Configuration Reference). 

PS： 还没有想到解决办法。。。。。


##  镜像说明

* 镜像仓库存放路径：`/registry`
* 镜像说明`.md`文件存放路径： `/repo_readme`
  该目录下存放文件均原样转载于 `https://hub.docker.com/explore/` 每个镜像的说明文件。
  请使用支持 `.md` 渲染的文件查看器[MarkDownPad](http://markdownpad.com/)。