# 虚拟化技术

## 为什么要使用虚拟化技术？

- 资源合理利用
- 节约成本

## 虚拟化技术的分类：

### 虚拟机技术：

虚拟出一条硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件

缺点：

- 资源占用十分多
- 冗余步骤多
- 启动慢

### 容器化技术

容器化技术不是模拟一个完整的操作系统，容器内的应用直接运行在宿主机的内核，容器是没有自己的内核的，也没有虚拟硬件，所以轻便

每个容器是相互隔离的，都有属于自己的文件系统，互不影响。

- 在操作系统内核上，允许多个隔离的用户空间
- 无需模拟硬件层
- 共享同一个宿主机的内核

#### container的核心技术

1.CGroup

- 限制容器的资源使用情况
- 调度多少个cpu时间片、占用多大内存、多少磁盘io等

2.NameSpace（隔离）

- UTS：主机名
- PID：进程号
- Network：ip、路由表、端口号
- Mount：挂在点、文件系统
- IPC：进程间通信（消息队列、共享内存）
- User：用户、用户组

3.chroot：文件系统隔离

##   win10怎么进入bios？

开始--->设置--->更新和安全--->恢复--->立即重启--->疑难解惑--->启动设置--->高级选项--->UEFI固体设置--->重启







  # docker

## docker概述

==docker≠容器，docker只是容器引擎==，是创建容器的工具。docker主要负责2件事：

- 负责容器的整个生命周期管理
- 负责本地容器镜像的构建和管理，同时配合镜像仓库，完成海量镜像的存储和管理

docker的理念：将应用和环境打包成一个镜像

Docker是基于go语言开发的开源项目。

Docker是==内核级别==的虚拟化，可以在一个物理机上运行很多的容器实例，服务器的性能可以被压榨到极致

 为什么使用docker？

- 搭建开发环境
- 搭建测试环境
- 搭建运维环境



## docker的基本组成

1. 客户端

2. 服务器：

   - 镜像：好比是一个模板，可以通过镜像来创建容器
   - 容器：==有了镜像才能创建容器==（可以把容器看做一个==简易的linux系统==）

3. 仓库：==存放镜像==的地方（docker hub，默认是国外的；阿里云等都有容器服务器，需配置镜像加速）

   - 公有仓库

   - 私有仓库

     ![docker](docker.assets/docker.jpg)



单机：docker+docker compose

多机：

- docker + docker compose +swarm
- docker+k8s



## Docker的底层原理

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过socket从客户端访问。Docker-Server接收到Docker-Client的命令后，就会执行这个命令



## docker安装

```shell
坑1：Problem: package docker-ce-3:19.03.8-3.el7.x86_64 requires containerd.io >= 1.2.2-3, but none of the providers can be ins:
解决办法：
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm


坑2：TLS handshake timeout.
解决办法：配置阿里云镜像加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://x36ir6k2.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



## docker常用命令

### 帮助命令

docker version：显示docker的版本信息

docker info：显示docker的系统信息，包括镜像和容器的数量
docker 命令  --help：帮助命令

systemctl start docker：启动docker

docker network ls：查看所有的docker网络

### 镜像命令

docker images： 查看主机上的镜像

- -q：只显示镜像id
- -a：显示所有镜像


docker search 镜像名：查找有哪些镜像

- -s ：点赞数

docker pull 镜像名[:版本号]：下载镜像

docker rmi 镜像名：删除镜像

- -f：强制删除
- docker rmi -f $(docker images -aq)：删除全部镜像

docker history：查看镜像时怎么生成

### 容器命令

==docker run==  [可选参数] 镜像名：==新建容器并启动==

- --name：容器名字，用来区分容器
- ==--net== 网络名：指定网络（若没有，则默认是docker0）
- ==-it==：启动==交互式==容器（进入容器查看内容）
- ==-d==：启动守护式容器（==后台==方式运行）
  - ==docker容器使用后台运行，就必须有一个前台进程==，否则docker发现没有应用，就会自动停止
- ==-p==：端口映射
  - -p 主机端口：容器端口（常用）
- -P：随机指定端口
- ==-v==：卷挂载

docker volume ls：查看所有volumn的情况

==docker ps==：查看docker上==正在运行==的容器

- -a：列出当前正在运行的容器+历史运行过的容器
- -q：只显示容器编号
- -n=?：显示最近创建的容器

#### 退出容器

exit：容器停止退出

ctrl+p+q：容器不停止退出

#### 删除容器

docker rm 容器id：删除容器

docker rm  -f $(docker ps -aq)：删除所有的容器

#### 启动和停止容器的操作

docker start 容器id：启动容器

docker restart 容器id：重启容器

docker stop 容器id：停止当前正在运行的容器

docker kill 容器id：强制停止当前容器

#### 其他常用命令

docker logs ：查看日志

- --tail ：要显示的日志条数
- -tf：显示日志

docker top 容器id：查看容器中进程信息

==docker inspect== 容器id：查看镜像的==元数据==

==docker exec== -it 容器id：进入当前正在运行的容器后开启一个新的终端（常用）

docker attach 容器id：进入容器正在执行的终端，不会启动新的进程

docker cp 容器id:容器内路径 目的地主机路径：从容器内拷贝文件到主机上

- 拷贝是一个手动过程，可以使用-v卷的技术，自动同步

docker commit -m=“提交的信息” -a=“作者” 容器id 目标镜像名： 将操作过的==容器==提交为一个==新的镜像==

- 若想保存当前容器的状态，就可以通过commit来提交，获得一个镜像
- 类似以前学习vm时的快照







## docker部署实战

### 部署nginx

1. 下载：docker pull nginx
2. 启动运行：docker run-d -p --name nginx01 3344:80 nginx
3. 浏览器中输入虚拟机网址+端口，是否出现欢迎界面
4. 进入nginx容器：docker exec -it nginx01 /bin/bash

### 部署tomcat

1. 下载：docker pull tomcat
2. 启动运行：docker run-d -p --name tomcat01 3355:8080 tomcat
3. 测试访问是否ok
4. 进入tomcat容器：docker exec -it tomcat01 /bin/bash
   - 发现linux命令少了；没有webapps。这是因为阿里云镜像默认为最小的镜像，所有不必要的都剔除掉，保证最小的可运行环境
     - 解决办法：cp -r webapps.dist/* webapps ，将webapps.dist下的文件拷贝到webapps中

### 安装mysql

1. 下载：
2. 启动运行并挂载卷：docker run -d -p 3333:3306 -v /home/zouhai/mysql/conf:/etc/mysql/conf.d -v /home/zouhai/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=199628 --name mysql01 mysql:5.7
3. 测试



## docker镜像原理

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件。它包含某个软件所需的所有内容（代码、运行时库、环境变量和配置文件）

- 即所有的应用直接打包docker镜像，就可以直接跑起来。
- 如何得到镜像：
  - 从远程仓库下载
  - 朋友拷贝
  - 自己制作一个镜像DockerFile

### ==联合文件系统==（层层叠加）

联合文件系统是一种分层、轻量级、高性能的文件系统。它支持对文件系统的修改作为一次提交来一层层叠加，同时可以把不同目录挂载到同一个虚拟文件系统下。联合文件系统是docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

### docker镜像加载原理

docker镜像是由一层层的文件系统组成（联合文件系统）

bootfs：主要包含bootloader和kernel。bootloader主要是引导加载kernel，linux刚启动时会加载bootfs文件系统，在==docker镜像的最底层是bootfs==。这一层和典型的linux系统是一致的，包括boot加载器和内核。当boot加载完成之后整个内核就在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs：在bootfs之上。包含的就是典型的linux系统中的/dev、/proc、/bin等标准目录和文件。rootfs就是不同操作系统的发行版，比如centos。

### 分层理解

所有的docker镜像都起始于一个==基础镜像层==，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

- 在添加额外镜像层的同时，镜像始终保持是当前所有镜像的组合。
- ==docker镜像都是只读的==，当容器启动时，一个新的==可写层（容器层）==被加载到镜像的顶部。==容器层之下的都叫镜像==。



## ==docker容器数据卷==

docker的理念是将应用和环境打包成一个镜像。如果数据都在容器中，那么容器删除，数据就会丢失。==数据持久化==。mysql容器删了，删库跑路。==mysql数据可以存储到本地==

容器之间可以有一个数据共享的技术，docker容器中产生的数据可以同步到本地，这就是==卷技术==（目录的挂载）。将容器内的目录挂载到linux中。

### 添加数据卷

1. 直接使用命令来挂载：
   - 指定路径挂载：==-v /宿主机目录:容器内目录==
   - 匿名挂载：-v 容器内目录
   - 具名挂载：==-v 卷名：容器内路径==。在没有指定目录情况下，所有docker容器数据卷都存放在/var/lib/docker/volumes/xxx/_data
     - ro：只读。只要看到ro就说明这个路径只能通过宿主机来操作，容器内部无法操作
     - rw：可读可写
2. DockerFile（经常使用，因为会构建自己的镜像）

   - 创建一个dockerfile文件，文件中的指令（==大写==） 参数
   - 生成镜像时自动挂载

### 数据卷容器（类似继承的父类）

数据卷容器：用于实现多个容器之间数据共享。--volumes-from 数据卷容器

使用场景：多个mysql实现数据共享



## ==Dockerfile==

### Dockerfile介绍

Dockerfile是用来构建docker镜像的构建文件（命令脚本）。镜像是一层层的，脚本是一个个的命令，每个命令都是一层。

Dockerfile是面向开发的，以后要发布项目，做镜像，就需要些Dockerfile文件

构建步骤：

1. 编写一个dockerfile文件

2. docker build -f dockerfile文件路径 -t 镜像名:[tag] ==.==：构建镜像

3. docker run：运行镜像

4. docker push：发布镜像（dockerhub、阿里云镜像仓库）

### Dockerfile构建过程

基础知识：

- 每个指令必须==大写==
- 自上到下执行
- #表注释
- 每个指令都会创建提交一个新的镜像层

Dockerfile指令：

- FROM：基础镜像，一切从这里开始构建
- MAINTAINER：镜像时谁写的
- RUN：镜像构建时需要运行的命令
- ADD：添加制作镜像所需的压缩包（自动解压）
- WORKDIR：镜像的工作目录
- VOLUME：挂载的目录
- EXPOSE：暴露端口配置
- CMD：指令容器启动时要运行的命令，只有最后一个会生效，可被替代
- ENTRYPOINT：指令容器启动时要运行的命令，可以直接==追加命令==，
- ONBUILD：当构建一个被继承dockerfile时就会运行此命令
- COPY：类似ADD，将文件拷贝到镜像中
- ENV：构建时设置环境变量

### Dockerfile制作tomcat镜像

1. 准备镜像文件：tomcat压缩包、jdk压缩包
2. 编写Dockerfile文件，官方命名Dockerfile，这样build时会自动寻找该文件，就不用-f指定了
3. 构建镜像：docker build -t 镜像名 ==.==
4. 启动镜像
5. 访问测试
6. 发布项目（由于做了卷挂载，可直接在本地编写项目就可以发布了）

### 发布自己的镜像

发布的镜像带上版本号



## ==Docker网络原理==

linux中获取当前ip地址：ip addr

docker中的所有网络接口都是==虚拟的==，因为虚拟的转发效率高（内网传递文件）

docker使用的是linux桥接，宿主机中是一个docker容器网桥（docker0）

NAT（直连）

### docker的网路模式

1. bridge：桥接（搭桥），docker默认的（自定义网络也使用==桥接==模式）
2. none：不配置网络
3. host：和宿主机共享网络
4. container：容器网络连接（用的少，局限大）

### docker0（相当于==路由器==）

先清空所有环境（镜像和容器），再研究docker网络原理

只要安装了docker，就会有一个网卡docker0（桥接模式），使用的是veth-pair技术。每启动一个容器，docker就会为容器分配一个ip。

- docker0特点：默认的，域名不能访问。不支持容器名连接，但通过--link可以打通
- veth-pair：是==一对虚拟设备接口==，一端连着协议、一端彼此相连。正因为有这个特性，veth-pair充当一个桥梁，连接各种虚拟网络设备
- 容器之间是可以ping通的。
- 只要容器删除，对应的一对网桥（veth-pair）就没了
- 所有容器不指定网络的情况下，都是docker0路由的，docker会给容器分配一个默认的可用ip


### 容器互联（通过==容器名==连接）：

1. --link：本质是在hosts配置中增加了一个映射，不建议使用

2. ==自定义网络==：
   - 自定义的网络docker已经帮我们维护好了对应的关系，推荐平时都使用自定义网络
   - ==docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 网络名==
     - --subnet：子网（192.168.0.0/16可支持65535个子网）
     - --gateway：网关（从哪出去）
   - 好处：不同集群使用不同网络，保证集群安全和健康

### 网络连通（==1个容器，2个ip==）

不同网段上的容器怎么连通？

- docker network connect  网络 容器名  （将容器连接到网络上）

- 若需跨网络操作别人，就需要使用docker network connect连通不同的网络

### 实战：部署redis集群

分片+高可用+负载集群







# Docker Compose（批量容器编排）

Docker Compose用来高效的管理容器。定义、运行多个容器。YML file配置文件

命令：

docker-compose up：启动项目（单机部署项目）

docker-compose up --build：重新构建项目

## yml配置文件

yml配置文件只有3层：

1. 版本
2. 服务配置
3. 其他配置（网络、卷、全局规则）





# Docker Swarm（集群）

raft协议：保证大多数节点存活才可以用，高可用

swarm：集群的管理和编号。docker可以初始化一个swarm集群，其他节点可以加入（管理、工作者）

node：一个docker节点。多个节点就组成了一个网络集群（管理、工作者）

service：服务，可以在管理节点或工作节点来运行

task：任务，容器内的命令

命令---管理---api---调度---工作节点（创建task容器）

## 弹性

## 动态扩缩容

- 容器

- 服务：

- 副本

灰度发布：金丝雀发布





# ==K8S==（集群）

k8s是基于==容器的集群管理平台==，一个k8s系统通常称为一个k8s集群（cluster），这个集群主要包括2部分：

- 1个Master节点（主节点）：
  - API Server
  - Scheduler
  - Controller manager
  - etcd
- 1群Node节点（计算节点）

k8s核心功能：

- 自我修复
- 服务发现和负载均衡
- 自动部署和回滚
- 弹性伸缩

云原生时代

云应用



## go语言是==并发==语言

入门

基础语法

高级对象

操作数据库

框架





# CI/CD Jenkins



{  "registry-mirrors": ["https://x36ir6k2.mirror.aliyuncs.com"] }



# DevOps（开发、运维）

一次构建、随处运行

- 更快速的交互和部署
- 更便捷的升级和扩缩容
- 更简单的系统运维
- 更高效的计算资源利用

微服务

容器化

CI：持续集成

CD：持续交付







# git+jenkins+jira自动化集成平台

## jenkins：持续集成（CI/CD）

jenkins插件：

- git
- jira

## jira：项目管理（bug管理）







# pytest+allure自动化测试报告

